# Implementar-uma-infraestrutura-automatizada-com-AWS-CloudFormation
**Este repositório documenta anotações e insights técnicos adquiridos durante a prática sobre AWS CloudFormation.**

**Objetivos: 🎯**
* Aplicar os conceitos aprendidos em um ambiente prático (hands-on).
* Documentar processos técnicos de forma clara e estruturada.
* Utilizar o GitHub como ferramenta para compartilhamento de documentação técnica.

**Conceitos: 💡**

O **AWS CloudFormation** é o principal serviço de Infraestrutura como Código (IaC) da AWS, que nos auxilia na automação e criação de recursos na AWS por meio de templates **JSON** ou **YAML**.

Ele nos ajuda a modelar e configurar nossos recursos da AWS para despendermos menos tempo gerenciando esses recursos e mais tempo nos concentrando em nossos aplicativos executados na AWS.

Criamos um modelo que descreve todos os recursos da AWS desejados, e o CloudFormation cuida do provisionamento e da configuração desses recursos. O CloudFormation está disponível por meio do console, da API, da AWS CLI, dos AWS SDKs e de várias integrações.

##Formato de modelo do CloudFormation

Como vimos podemos criar modelos do CloudFormation nos formatos JSON ou YAML. Ambos os formatos atendem ao mesmo propósito, mas oferecem vantagens distintas em termos de legibilidade e complexidade.

**Json:**
O JSON é o formato tradicional e originalmente suportado pelo CloudFormation, é um formato leve de intercâmbio de dados que é fácil de ser analisado e gerado por computadores, porém para seres humanos ele pode ser complicado de ler e escrever. Ele é estruturado baseado em pares chave-valor, e usa chaves {} e colchetes [] para definir recursos, parâmetros e outros componentes. Sua sintaxe requer uma declaração explícita de cada elemento, o que pode tornar o modelo extremamente detalhado, mas garante a adesão estrita a um formato estruturado.

**YAML:**
O  YAML foi projetado para ser mais legível por humanos e menos detalhado do que o JSON, devido a isso é o formato preferido por muitos. Ele usa o recuo como forma de indentação, o que facilita a visualização da hierarquia de recursos e parâmetros. No entanto, a dependência do YAML do uso de recuos pode levar a erros se o espaçamento não for consistente, o que requer atenção cuidadosa para manter a precisão.


## Estrutura do modelo

Os modelos do CloudFormation são divididos em seções diferentes, e cada seção se destina a conter um tipo específico de informação. A ordem delas no arquivo YAML (ou JSON) geralmente segue uma convenção lógica. Ao criar os modelos não devamos usar seções importantes duplicadas, por exemplo, a seção Resources. Embora o CloudFormation possa aceitar o modelo, ele terá um comportamento indefinido ao processá-lo e poderá provisionar recursos incorretamente ou retornar erros inexplicáveis.

**1. AWSTemplateFormatVersion (Opcional):**

Declara a versão do formato do template.
Exemplos:

JSON: ```"AWSTemplateFormatVersion" : "2010-09-09"```

YAML: ```AWSTemplateFormatVersion: '2010-09-09'```

**2. Description (Opcional):**

Um campo de texto livre para descrever o que o template faz, sendo essencial para documentação.

Exemplo:

JSON: ```"Description" : "Este template cria um bucket S3 privado."```

YAML: ```Description: 
        Este template cria um bucket S3 privado.```

**3. Parameters (Opcional):**

Define os "inputs" ou "perguntas" que o usuário deve responder ao criar a stack, ele torna o template reutilizável, pois ao invés de usar valores hardcode como "t2.micro", criamos um parâmetro InstaceType e o usuário pode escolher t2.micro ou t3.small no momento do deploy.

Exemplo: 

JSON: ```"Parameters" : {
    set of parameters
  },```
  
YAML: ```Parameters:
        set of parameters```

**4. Mappings (Opcional)**

Uma tabela de consulta (chave-valor) estática. Permite que usemos a lógica baseada em um valor de entrada. O uso mais comum é selecionar a AMI (Imagem de Máquina) correta com base na região.

Exemplo:

JSON:   ```"Mappings" : {
    set of mappings
  },```
  
YAML: ```Mappings:
        set of mappings```

**5. Conditions (Opcional):**

Define regras lógicas (if/then) que podem ser avaliadas durante a criação da stack, ou seja cria recursos condicionalmente, por exemplo, "só crie um Volume EBS de 100GB SE o parâmetro EnvironmentName for igual a prod".

Exemplo:

JSON: ```"Conditions" : {
    set of conditions
  },```
  
YAML: ```Conditions:
        set of conditions```

**6. Resources (Obrigatório):**

A seção mais importante do template, onde devemos declarar todos os recursos da AWS que desejamos criar (instâncias EC2, buckets S3, tabelas DynamoDB, Security Groups, etc.).

Exemplo:

JSON: ```"Resources" : {
    set of resources
  },```
  
YAML: ```Resources:
       set of resources```

**7. Outputs (Opcional):**

Declara valores que você quer que sejam exibidos quando a stack terminar de ser criada. Ele serve para obter informações úteis (como o ID da instância criada, o DNS público de um servidor web, ou o ARN de um bucket) sem ter que procurá-las no console, serve também para exportar valores para que outras stacks possam usá-los.

Exemplo:

JSON: ```"Outputs" : {
    set of outputs
  }```
  
YAML: ```Outputs:
        set of outputs```

**O fluxo lógico é: o usuário fornece Parameters, o template usa Mappings e Conditions para decidir como e se deve criar os Resources, e no final, ele mostra os Outputs.**


