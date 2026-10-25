# Laboratório AWS — Criação de uma VPC e Implantação de um Servidor Web

## 📌 Sobre o laboratório

Este laboratório prático tem como objetivo criar uma rede virtual na AWS utilizando o Amazon VPC, configurar sub-redes públicas e privadas, definir tabelas de rotas, configurar um grupo de segurança e iniciar uma instância EC2 para hospedar um servidor web.

**Curso:** Computação em Nuvem  
**Plataforma:** Amazon Web Services (AWS)  
**Duração estimada:** 45 minutos

## 🎯 Objetivos

- Criar uma Amazon VPC.
- Configurar sub-redes públicas e privadas.
- Configurar tabelas de rotas.
- Entender o funcionamento do Internet Gateway e do NAT Gateway.
- Criar um grupo de segurança.
- Iniciar uma instância EC2.
- Instalar e configurar um servidor web.
- Testar o acesso à aplicação pelo navegador.

## 🖥️ 1. Diagrama da arquitetura

O diagrama apresenta a estrutura de rede utilizada como referência para o laboratório.

![01 - Diagrama Cliente](imagens/01%20-%20Diagrama%20Cliente.png)

## 🌐 2. Criar a Amazon VPC

Uma **Amazon VPC (Virtual Private Cloud)** permite criar uma rede virtual isolada dentro da AWS, na qual podemos configurar endereços IP, sub-redes, tabelas de rotas e controles de acesso.

Documentação oficial: https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html

![02 - Criar VPC](imagens/02%20-%20Criar%20VPC.png)

### Configuração da VPC

| Configuração | Valor |
|---|---|
| Nome | Lab VPC |
| Bloco CIDR IPv4 | `10.0.0.0/16` |
| IPv6 | Não habilitado |
| Zonas de disponibilidade iniciais | 1 |
| Sub-rede pública inicial | `10.0.0.0/24` |
| Sub-rede privada inicial | `10.0.1.0/24` |
| NAT Gateway | 1, em uma zona de disponibilidade |
| Endpoints da VPC | Nenhum |

A VPC utiliza o bloco CIDR `10.0.0.0/16`, que permite organizar os endereços IP das sub-redes dentro da rede virtual.

![03 - Criar fluxo de trabalho de VPC](imagens/03%20-%20Criar%20fluxo%20de%20trabalho%20de%20VPC.png)

### Resultado da criação

Após concluir a configuração, a VPC será criada com os recursos de rede selecionados.

![04 - Lab VPC](imagens/04%20-%20Lab%20VPC.png)

## 🧩 3. Configurar as sub-redes

As sub-redes dividem a VPC em segmentos menores de rede. Elas podem ser associadas a diferentes tabelas de rotas para controlar como o tráfego circula.

### Sub-redes iniciais

| Nome | Bloco CIDR | Finalidade |
|---|---|---|
| Public Subnet 1 | `10.0.0.0/24` | Sub-rede pública |
| Private Subnet 1 | `10.0.1.0/24` | Sub-rede privada |

![05 - Sub-redes](imagens/05%20-%20Sub-redes.png)

## 🛣️ 4. Configurar as tabelas de rotas

As tabelas de rotas determinam para onde o tráfego de rede é encaminhado.

Neste laboratório, serão utilizadas duas tabelas de rotas:

- **Public Route Table:** utilizada pelas sub-redes públicas.
- **Private Route Table:** utilizada pelas sub-redes privadas.

A tabela de rotas pública poderá encaminhar tráfego para a internet por meio do Internet Gateway. A tabela privada utilizará as rotas configuradas para os recursos privados, incluindo o NAT Gateway quando aplicável.

![06 - Tabelas de rotas](imagens/06%20-%20Tabelas%20de%20rotas.png)

## 🌍 5. Configurar o Internet Gateway

O Internet Gateway permite a comunicação entre recursos da VPC e a internet quando as rotas e os endereços IP necessários estão configurados.

Neste laboratório, ele será utilizado para possibilitar o acesso externo ao servidor web localizado em uma sub-rede pública.

![07 - Gateways da internet](imagens/07%20-%20Gateways%20da%20internet.png)

## 📡 6. Endereços IP elásticos

Um endereço IP elástico é um endereço IPv4 público que pode ser associado a determinados recursos da AWS, conforme a configuração utilizada.

Neste laboratório, o endereço IP elástico está relacionado à configuração do NAT Gateway.

![08 - Endereços IP elásticos](imagens/08%20-%20Endereços%20IP%20elásticos.png)

## 🔀 7. Configurar o NAT Gateway

O NAT Gateway permite que recursos em sub-redes privadas iniciem conexões com destinos externos, sem precisar aceitar conexões iniciadas diretamente da internet.

A configuração deve seguir a arquitetura proposta no laboratório e utilizar a tabela de rotas privada para encaminhar o tráfego externo por meio do NAT Gateway, quando aplicável.

![09 - Gateways NAT](imagens/09%20-%20Gateways%20NAT.png)

## 🧱 8. Criar sub-redes adicionais

Na próxima etapa, serão criadas mais duas sub-redes em uma segunda zona de disponibilidade.

| Nome | Bloco CIDR | Finalidade |
|---|---|---|
| Public Subnet 2 | `10.0.2.0/24` | Sub-rede pública |
| Private Subnet 2 | `10.0.3.0/24` | Sub-rede privada |

Essas sub-redes ampliam a organização da rede e permitem distribuir recursos entre zonas de disponibilidade.

![10 - Criar sub-redes adicionais](imagens/10%20-%20Tarefa%202%20Criar%20sub-redes%20adicionais.png)

## 🔗 9. Associar as sub-redes às tabelas de rotas

Depois de criar as sub-redes adicionais, é necessário associá-las às tabelas de rotas correspondentes.

| Sub-rede | Tabela de rotas |
|---|---|
| Public Subnet 1 | Public Route Table |
| Public Subnet 2 | Public Route Table |
| Private Subnet 1 | Private Route Table |
| Private Subnet 2 | Private Route Table |

A associação correta garante que cada sub-rede utilize as rotas apropriadas para sua finalidade.

![11 - Associar sub-redes e adicionar rotas](imagens/11%20-%20Tarefa%203%20Associar%20as%20sub-redes%20e%20adicionar%20rotas.png)

## 🔐 10. Criar um grupo de segurança

O grupo de segurança controla o tráfego permitido para as instâncias associadas a ele.

Neste laboratório, será criado o grupo de segurança com as seguintes configurações:

| Configuração | Valor |
|---|---|
| Nome | Web Security Group |
| VPC | Lab VPC |
| Regra de entrada | HTTP |
| Protocolo | TCP |
| Porta | 80 |
| Origem | `0.0.0.0/0` |

A regra HTTP permite que clientes externos acessem o servidor web pela porta 80.

**Observação de segurança:** permitir acesso de qualquer endereço IPv4 é adequado apenas para o cenário de teste previsto no laboratório. Em ambientes reais, restrinja as origens conforme a necessidade. Não abra portas administrativas para toda a internet.

![12 - Criar grupo de segurança VPC](imagens/12%20-%20Tarefa%204%20-%20Criar%20um%20grupo%20de%20segurança%20VPC.png)

## 🖥️ 11. Iniciar uma instância de servidor web

Nesta etapa, será criada uma instância EC2 para hospedar a aplicação web.

### Configurações da instância

| Configuração | Valor |
|---|---|
| Nome | Web Server 1 |
| Imagem de máquina (AMI) | Amazon Linux 2 |
| Tipo de instância | `t3.micro` |
| Par de chaves | `vockey` |
| VPC | Lab VPC |
| Sub-rede | Public Subnet 2 |
| Atribuição automática de IP público | Habilitada |
| Grupo de segurança | Web Security Group |

A instância será iniciada na sub-rede pública adicional e utilizará o grupo de segurança criado na etapa anterior.

### Script de inicialização do servidor web

No campo de dados do usuário (*User data*) da instância EC2, utilize o script abaixo para instalar os componentes necessários e iniciar o servidor web:

```bash
#!/bin/bash
yum install -y httpd mysql php
wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-100-RESTRT-1/267-lab-NF-build-vpc-web-server/s3/lab-app.zip
unzip lab-app.zip -d /var/www/html/
chkconfig httpd on
service httpd start
```

O script executa as seguintes tarefas:

1. Instala o Apache HTTP Server, o cliente MySQL e o PHP.
2. Baixa o arquivo compactado da aplicação.
3. Extrai os arquivos da aplicação para `/var/www/html/`.
4. Configura o serviço HTTP para iniciar automaticamente.
5. Inicia o servidor web.

**Importante:** o script pressupõe que a instância consegue acessar a internet para baixar os pacotes e o arquivo da aplicação. A configuração da rede e das rotas precisa permitir essa comunicação.

![13 - Iniciar instância de servidor web](imagens/13%20-%20Tarefa%205%20-%20Iniciar%20uma%20instância%20de%20servidor%20web.png)

## ✅ 12. Verificar a instância EC2

Depois de iniciar a instância:

1. Acesse o console do Amazon EC2.
2. Localize a instância `Web Server 1`.
3. Aguarde até que as verificações de status indiquem **2/2 verificações aprovadas**.
4. Consulte os detalhes da instância.
5. Localize o campo **DNS IPv4 público** (*Public IPv4 DNS*).
6. Copie o endereço DNS público.
7. Abra um navegador e cole o endereço na barra de navegação, utilizando HTTP.

Exemplo de formato do endereço:

```text
http://ec2-xx-xx-xx-xx.compute-1.amazonaws.com
```

O endereço acima é apenas um exemplo de formato. Utilize o DNS público real exibido na sua instância.

Se a aplicação não carregar, verifique o estado da instância, as regras de entrada do grupo de segurança, a tabela de rotas, o Internet Gateway, a atribuição do IP público e a execução do script de inicialização.

## 🏁 13. Resultado final

Ao concluir as etapas, o laboratório terá uma VPC configurada com sub-redes públicas e privadas, tabelas de rotas, conectividade de rede e um grupo de segurança. Uma instância EC2 terá sido iniciada para hospedar o servidor web.

![14 - Laboratório concluído](imagens/14%20-%20FIM.png)

## 📚 Conceitos praticados

- **Amazon VPC:** rede virtual isolada na AWS.
- **CIDR:** notação utilizada para representar blocos de endereços IP.
- **Sub-rede pública:** sub-rede configurada para permitir conectividade com a internet, conforme as rotas e os endereços utilizados.
- **Sub-rede privada:** sub-rede destinada a recursos que não precisam receber conexões diretamente da internet.
- **Tabela de rotas:** define o encaminhamento do tráfego de rede.
- **Internet Gateway:** permite a comunicação entre a VPC e a internet quando as rotas estão configuradas.
- **NAT Gateway:** possibilita conexões de saída de recursos privados para destinos externos.
- **Endereço IP elástico:** endereço IPv4 público alocável na AWS.
- **Grupo de segurança:** controla o tráfego de entrada e saída permitido para recursos associados.
- **Amazon EC2:** serviço de computação que permite executar instâncias virtuais.
- **Apache HTTP Server:** software utilizado para disponibilizar páginas e aplicações web.

## 📝 Conclusão

Este laboratório permitiu praticar conceitos fundamentais de computação em nuvem na AWS, desde a criação e organização de uma rede virtual até a implantação de um servidor web em uma instância EC2.

A atividade reforça a importância de configurar corretamente as sub-redes, as tabelas de rotas, os gateways e as regras de segurança para que os recursos da nuvem possam se comunicar conforme o planejado.

## 📂 Estrutura do repositório

Mantenha o arquivo `README.md` na raiz do repositório e as 14 imagens dentro da pasta `imagens/`, com estes nomes:

```text
.
├── README.md
└── imagens/
    ├── 01 - Diagrama Cliente.png
    ├── 02 - Criar VPC.png
    ├── 03 - Criar fluxo de trabalho de VPC.png
    ├── 04 - Lab VPC.png
    ├── 05 - Sub-redes.png
    ├── 06 - Tabelas de rotas.png
    ├── 07 - Gateways da internet.png
    ├── 08 - Endereços IP elásticos.png
    ├── 09 - Gateways NAT.png
    ├── 10 - Tarefa 2 Criar sub-redes adicionais.png
    ├── 11 - Tarefa 3 Associar as sub-redes e adicionar rotas.png
    ├── 12 - Tarefa 4 - Criar um grupo de segurança VPC.png
    ├── 13 - Tarefa 5 - Iniciar uma instância de servidor web.png
    └── 14 - FIM.png
```
