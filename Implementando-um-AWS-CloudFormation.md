## Implantando um AWS CloudFormation

**Objetivo:**

Criar uma instância do Amazon EC2 (t2.micro) em uma Zona de Disponibilidade (AZ) específica, definida pelo usuário.


**Passo 1: Escrever o template**

YAML:

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: >-
  Template para criar uma EC2 (t2.micro) em uma 
  Zona de Disponibilidade (AZ) específica.

Parameters:
  KeyName:
    Description: 'OBRIGATÓRIO: O nome do seu Par de Chaves (Key Pair) para SSH.'
    Type: AWS::EC2::KeyPair::KeyName
    ConstraintDescription: 'Deve ser o nome de uma Key Pair existente.'
  
  TargetAvailabilityZone:
    Description: 'A Zona de Disponibilidade onde a instância será criada (ex: us-east-1a).'
    Type: AWS::EC2::AvailabilityZone::Name
    ConstraintDescription: 'Deve ser um nome de AZ válido na região atual.'

  LatestAmiId:
    Description: '(Automático) ID da Imagem (AMI) mais recente do Amazon Linux 2.'
    Type: 'AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>'
    Default: '/aws/service/amzn2/amzn2-ami-hvm-x86_64-gp2'

Resources:
  MySecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: 'Permite acesso SSH na porta 22'
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0 

  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro 
      KeyName: !Ref KeyName       
      ImageId: !Ref LatestAmiId   
      AvailabilityZone: !Ref TargetAvailabilityZone 
      SecurityGroupIds:
        - !Ref MySecurityGroup 
      Tags:
        - Key: Name
          Value: !Sub 'Instancia-em-${TargetAvailabilityZone}'

Outputs:
  ConnectTo:
    Description: 'Use este DNS para se conectar via SSH'
    Value: !GetAtt MyEC2Instance.PublicDnsName
```

JSON:

```json
{
  "AWSTemplateFormatVersion": "2010-09-09",
  "Description": "Template para criar uma EC2 (t2.micro) em uma Zona de Disponibilidade (AZ) específica.",
  "Parameters": {
    "KeyName": {
      "Description": "OBRIGATÓRIO: O nome do seu Par de Chaves (Key Pair) para SSH.",
      "Type": "AWS::EC2::KeyPair::KeyName",
      "ConstraintDescription": "Deve ser o nome de uma Key Pair existente."
    },
    "TargetAvailabilityZone": {
      "Description": "A Zona de Disponibilidade onde a instância será criada (ex: us-east-1a).",
      "Type": "AWS::EC2::AvailabilityZone::Name",
      "ConstraintDescription": "Deve ser um nome de AZ válido na região atual."
    },
    "LatestAmiId": {
      "Description": "(Automático) ID da Imagem (AMI) mais recente do Amazon Linux 2.",
      "Type": "AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>",
      "Default": "/aws/service/amzn2/amzn2-ami-hvm-x86_64-gp2"
    }
  },
  "Resources": {
    "MySecurityGroup": {
      "Type": "AWS::EC2::SecurityGroup",
      "Properties": {
        "GroupDescription": "Permite acesso SSH na porta 22",
        "SecurityGroupIngress": [
          {
            "IpProtocol": "tcp",
            "FromPort": 22,
            "ToPort": 22,
            "CidrIp": "0.0.0.0/0"
          }
        ]
      }
    },
    "MyEC2Instance": {
      "Type": "AWS::EC2::Instance",
      "Properties": {
        "InstanceType": "t2.micro",
        "KeyName": {
          "Ref": "KeyName"
        },
        "ImageId": {
          "Ref": "LatestAmiId"
        },
        "AvailabilityZone": {
          "Ref": "TargetAvailabilityZone"
        },
        "SecurityGroupIds": [
          {
            "Ref": "MySecurityGroup"
          }
        ],
        "Tags": [
          {
            "Key": "Name",
            "Value": {
              "Fn::Sub": "Instancia-em-${TargetAvailabilityZone}"
            }
          }
        ]
      }
    }
  },
  "Outputs": {
    "ConnectTo": {
      "Description": "Use este DNS para se conectar via SSH",
      "Value": {
        "Fn::GetAtt": [
          "MyEC2Instance",
          "PublicDnsName"
        ]
      }
    }
  }
}
```


**Passo 2: Implementação**

### Pelo console da AWS:
Esta é a forma mais simples, basicamento fazemos o upload do nosso arquivo.

1. No console da AWS, acessar o serviço CloudFormation.

2. Clicar no botão "Criar stack" -> "Com novos recursos (padrão)".

3. Em "Preparar template", escolher "O template está pronto".

4. Em "Especificar template", escolher "Fazer upload de um arquivo de template".

5. Clicar em "Escolher arquivo" e selecionar o ec2-com-az.yaml. Clicar em "Avançar".

6. Nomear a stack (ex: meu-servidor-az-A).

7. Na seção Parameters, o CloudFormation lerá o template e mostrará os parâmetros:

- KeyName: Selecionar seu par de chaves na lista.

- TargetAvailabilityZone: Selecionar a Zona de Disponibilidade (ex: us-east-1a ou sa-east-1a, dependendo da sua região).

8. Clicar em "Avançar" nas próximas telas e, no final, revisar e clicar em "Criar stack".


### Pela AWS CLI:
Esta é a forma usada em scripts e pipelines de automação, é mais profissional.

1. Certifique-se de ter o AWS CLI instalado e configurado (com o comando aws configure).

2. Abrir o terminal e navegar até a pasta onde o arquivo ec2-com-az.yaml está salvo.

3. Executar o comando deploy. Ele criará a stack se ela não existir ou a atualizará se ela já existir.

```bash
aws cloudformation deploy \
    --template-file ec2-com-az.yaml \
    --stack-name meu-servidor-az-B \
    --parameter-overrides KeyName=sua-chave-aqui TargetAvailabilityZone=us-east-1b
```

**Atenção: Lembrar de trocar sua-chave-aqui pelo nome real do seu Key Pair e us-east-1b pela Zona de Disponibilidade que você desejará usar.**
