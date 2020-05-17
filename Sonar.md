# SONAR

## O que é?
* Plataforma _open source_ que permite gerenciar a qualidade do código;
* Mantém análise hitórica da codificação;

## acesso a Ferramenta
http://sonar.compasso.com.br/dashboard?id=MX

## Credenciais 
Verfificar wiki https://compasso.atlassian.net/wiki/spaces/DEV/pages/2617535/Introdu+o+ao+Sonar#Introdu%C3%A7%C3%A3oaoSonar-Acesso%C3%A0ferramenta

## Situação atual do projeto
Na tela principal será apresentada a situação atual do projeto. Onde são apresentados os indicadores:
* Bugs e vulnerabilidade em potencial;
* Pontos de problemas em potencial;
* Cobertura de testes unitários;
* Duplicaçao de código.

# Arquitetura
[1] A plataforma do SonaQube é composta por 4 componentes: 
   1. Um servidor SonarQube iniciando 3 processos principais:
       1. um servidor Web para desenvolvedores;
       2. um servidor de pesquisa baseado no Elasticsearch;
       3. um servidor do Compute Engine encarregado de processar os relatórios de análise de código e salvá-los no banco de dados SonarQube
    2. Um banco de dados SonarQube para armazenar:
       1. a configuração da instância do SonarQube (segurança, configurações de plugins, etc.)
       2. os instantâneos de qualidade de projetos, visualizações etc.
    3. Vários plug-ins do SonarQube instalados no servidor;
    4. Um ou mais scanners SonarQube em execução nos servidores de integração de compilação / contínua para analisar projetos

![Arquitetura SonaQube](https://docs.sonarqube.org/6.7/images/download/attachments/11634448/image2017-10-3113_8_50.png)

# Integração
O esquema a seguir mostra como o SonarQube se integra a outras ferramentas do ALM e onde os vários componentes do SonarQube são usados
![Integração SonarQube](https://docs.sonarqube.org/6.7/images/download/attachments/11634448/image2017-10-3113_9_10.png)
   1. Os desenvolvedores codificam em seus IDEs e usam o _SonarLint_ para executar análises locais.
   2. Os desenvolvedores introduzem seu código no seu SCM favorito: git, SVN, TFVC, ...
   3. O Continuous Integration Server aciona uma compilação automática e a execução do SonarQube Scanner é necessária para executar a análise do SonarQube.
   4. O relatório de análise é enviado ao servidor SonarQube para processamento.
   5. O SonarQube Server processa e armazena os resultados do relatório de análise no banco de dados SonarQube e exibe os resultados na interface do usuário.
   6. Os desenvolvedores revisam, comentam suas issues para gerenciar e reduzir seu débito técnica por meio da interface do usuário do SonarQube.
   7. Os Managers recebem relatórios da análise.
       1. As operações usam APIs para automatizar a configuração e extrair dados do SonarQube.
       2. As operações usam o JMX para monitorar o SonarQube Server.

## Sobre máquinas e locais
* A plataforma SonarQube não pode ter mais de um servidor SonarQube e um banco de dados SonarQube.
* Para um desempenho ideal, cada componente (servidor, banco de dados, scanners) deve ser instalado em uma máquina separada e a máquina do servidor deve ser dedicada.
* Os scanners SonarQube são dimensionados adicionando máquinas.
* Todas as máquinas devem estar sincronizadas com horário.
* O servidor SonarQube e o banco de dados SonarQube devem estar localizados na mesma rede
* Os scanners SonarQube não precisam estar na mesma rede que o servidor SonarQube.
* Não há comunicação entre os scanners SonarQube e o banco de dados SonarQube .

## Troubleshooting
![Troubleshooting](https://docs.sonarqube.org/6.7/Troubleshooting.html)


# Analisando o código fonte 

## Escopo da análise: tipos de arquivos e dados
O SonarQube pode realizar análises em mais de 20 linguagens diferentes. O resultado dessa análise serão medidas de qualidade e problemas (casos em que as regras de codificação foram violadas). No entanto, o que é analisado variará dependendo do linguagem:
   * Em todos as linguagens, os dados de "blame" serão importados automaticamente de provedores de SCM compatíveis. Git e SVN são suportados automaticamente. Outros provedores requerem plugins adicionais.
   * Em todas as linguagens, é realizada uma análise estática do código-fonte (arquivos Java, programas COBOL etc.)
   * Uma análise estática do código compilado pode ser realizada para certas linguagens ( arquivos .class em Java, arquivos .dll em C # etc.)
   * Uma análise dinâmica do código pode ser realizada em determinadas linguagens.

### Arquivos não reconhecidos
Por padrão, apenas os arquivos reconhecidos por um plug-in de linguagem são carregados no projeto durante a análise. Por exemplo, se sua instância do SonarQube tiver os plug-ins Java e JavaScript, todos os arquivos .java e .js serão carregados, mas os arquivos .xml serão ignorados. No entanto, é possível importar todos os arquivos de texto na codificação de análise em um projeto, definindo **Settings > Exclusions > Files > Import unknown files to true.**

## Durante a análise
Durante a análise, os dados são solicitados ao servidor, os arquivos fornecidos para a análise são analisados ​​e os dados resultantes são enviados de volta ao servidor no final na forma de um relatório, que é analisado de forma assíncrona no servidor.

Os relatórios de análise são enfileirados e processados ​​sequencialmente; portanto, é possível que por um breve período após o registro de análise mostre a conclusão, os valores atualizados não sejam visíveis no seu projeto SonarQube. No entanto, você poderá dizer o que está acontecendo, porque um ícone será adicionado ao lado do nome do projeto. Passe o mouse sobre ele para obter mais detalhes (e links se você estiver conectado com as permissões apropriadas.)

# Analise do código
Havendo uma instalação da plataforma SonarQube, você estará pronto para instalar um scanner e começar a criar projetos. Para fazer isso, você deve instalar e configurar o scanner mais adequado às suas necessidades [1].  

## Quais arquivos serão analisados?
Por padrão, apenas os arquivos reconhecidos por um analisador de linguagem são carregados no projeto durante a análise. Por exemplo, se você estiver usando o SonarQube Community Edition, que inclui análise de Java e JavaScript, mas não C ++, todos os arquivos .java e .js serão carregados, mas os arquivos .cpp serão ignorados.

## Executando análise
Primeiro, você deve instalar o(s) plugin(s) para a(s) linguagem(ns) do projeto a ser analisado, por um download direto ou através do Marketplace.

Então, você precisa escolher um método de análise. Estão disponíveis os seguintes:
* SonarQube Scanner for MSBuild : análise de lançamento de projetos .Net
* SonarQube Scanner for Maven : inicie a análise do Maven com configuração mínima
* SonarQube Scanner for Gradle : inicie a análise Gradle
* SonarQube Scanner for Ant : análise de lançamento a partir do Ant
* **SonarQube Scanner para Jenkins**: Iniciar análise a partir do Jenkins
* SonarQube Scanner : Inicie a análise a partir da linha de comando quando nenhum dos outros analisadores for apropriado

Observe que não recomendamos a execução de um scanner antivírus na máquina em que a análise do SonarQube é executada; isso pode resultar em comportamento imprevisível.

## For More Information
See also:
Analysis Parameters : https://docs.sonarqube.org/6.7/AnalysisParameters.html
Project Examples: https://docs.sonarqube.org/6.7/SonarQubeProjectExamples.html
Generic Test Data: https://docs.sonarqube.org/6.7/GenericTestData.html


# SonarScanner for Jenkins

Este plug-in permite centralizar a configuração dos detalhes de conexão do servidor SonarQube na configuração global do Jenkins.

Em seguida, você pode acionar a análise do SonarQube a partir do Jenkins usando o _Jenkins Build Steps_ ou _Jenkins Pipeline DSL_ para acionar a análise com:
* SonarScanner
* SonarScanner for Maven
* SonarScanner for Gradle
* SonarScanner for MSBuild

Após a conclusão do Job, o plug-in detectará que uma análise do SonarQube foi feita durante o build e exibirá um icone e um widget na página do job com um link para o painel do SonarQube, além do _quality gate status._

### Analisando uma soluação .NET
[...]

### Analisando um projeto Java com Maven ou Gradle
_injection of SonarQube server configuration as build environment variables_ está desmarcado.
[...]

### Analisando outro tipos de projetos
#### Configuração do Job
1. Configure o projeto e vá para a seção Build.
2. Adicione a etapa de buid do SonarScanner à sua etapa de build.
3. Configure as propriedades de análise do SonarQube. Você pode apontar para um arquivo sonar-project.properties existente ou definir as propriedades da análise diretamente no campo Analysis properties.

### Usando um pipeline Jenkins
É fornecido um bloco _withSonarQubeEnv_ que permite selecionar o servidor SonarQube com o qual você deseja interagir. Os detalhes da conexão foram configurados no Jenkins, em _Global Configuration_ serão transmitidos automaticamente para o scanner.

Se necessário, você pode substituir o _credentialId_ se você não quiser usar o definido na configuração global (por exemplo, se você definir credenciais no nível da pasta).

Aqui estão alguns exemplos para cada scanner, supondo que você execute em escravos Unix e tenha configurado um servidor chamado "My SonarQube Server", além das ferramentas necessárias. Se você executar em escravos do Windows, substitua sh por bat.

**SonarScanner:**
~~~groovy
node {
  stage('SCM') {
    git 'https://github.com/foo/bar.git'
  }
  stage('SonarQube analysis') {
    def scannerHome = tool 'SonarScanner 4.0';
    withSonarQubeEnv('My SonarQube Server') { // If you have configured more than one global server connection, you can specify its name
      sh "${scannerHome}/bin/sonar-scanner"
    }
  }
}
~~~

# SonarQube Scanner 2.11
[5] Este plug-in permite a fácil integração do SonarQube ™, a plataforma de código aberto para Inspeção Contínua da qualidade do código.

## Token de autenticação do servidor armazenado em texto sem formatação
[5.1] **SECURITY-1163 / CVE-2018-1000425**
O SonarQube Scanner Plugin armazenou um token de autenticação do servidor não criptografado em seu arquivo de configuração global no Jenkins master. Esse token pode ser visualizado por usuários com acesso ao sistema de arquivos mestr

O plug-in agora armazena o token criptografado nos arquivos de configuração em disco e não o transfere mais para os usuários que visualizam o formulário de configuração em texto sem formatação

# SonarQube Scanner for Jenkins
[4] 
## waitForQualityGate: Aguarde a conclusão da análise do SonarQube e retorne o status do portão de qualidade
[4] Esta etapa interrompe a execução do Pipeline e aguarda a conclusão da análise do SonarQube enviada anteriormente e retorna o status do quality gate. Definir o parâmetro _abortPipeline_ como _true_ abortará o pipeline se o status do _quality gate_ não estiver verde.

:exclamation: Nota: Esta etapa não requer um executor.

### Requisitos
* SonarQube server 6.2+ 
* Configure um webhook no servidor SonarQube apontando para <sua instância do Jenkins>/sonarqube-webhook/. A barra final é obrigatória! 
* Use o step _withSonarQubeEnv_ para executar sua análise antes de usar esta etapa

#### Exemplo 
usando pipeline declarativo:

~~~groovy
pipeline {
        agent none
        stages {
          stage("build & SonarQube analysis") {
            agent any
            steps {
              withSonarQubeEnv('My SonarQube Server') {
                sh 'mvn clean package sonar:sonar'
              }
            }
          }
          stage("Quality Gate") {
            steps {
              timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
            }
          }
        }
      }
      
~~~

usando pipeline scrpited:

~~~goovy

      stage("build & SonarQube analysis") {
          node {
              withSonarQubeEnv('My SonarQube Server') {
                 sh 'mvn clean package sonar:sonar'
              }
          }
      }

      stage("Quality Gate"){
          timeout(time: 1, unit: 'HOURS') {
              def qg = waitForQualityGate()
              if (qg.status != 'OK') {
                  error "Pipeline aborted due to quality gate failure: ${qg.status}"
              }
          }
      }
~~~
      
* abortPipeline
    * Type: boolean
* credentialsId (optional)
    * Type: String
* webhookSecretId (optional)
    * Type: String

### withSonarQubeEnv: Preparar o ambiente do SonarQube Scanner
* installationName
    * Type: String
* credentialsId (optional)
    * Type: String


# Parâmetros de análise
[6] 
## Configurações padrão

|  KEY                  | DESCRIPTION       | DEFAULT                                                   |
|-------------------|-------------------|---------------------------------------------------------- |
| **sonar.host.url**    | o URL do servidor | http://localhost:9000                                     |
| **sonar.login**       | O token de login ou autenticação de um usuário do SonarQube com permissão Execute Analysis no projeto.| 
| **sonar.sources**     | Caminhos separados por vírgula para diretórios que contêm os principais aquivos fontes. | Leia a partir do sistema de compilação para projetos Maven, Gradle e MSBuild. O padrão é o diretório base do projeto quando nem sonar.sources nem sonar.tests são fornecidos. | |
| **sonar.forceAnalysis** | para forçar uma analise, mas quando a analise anterior falhou[6.1] | |
| **sonar.sourceEncoding** | Codificação dos arquivos de origem. Ex: UTF-8, MacRoman, Shift_JIS. Esta propriedade pode ser substituída pela propriedade padrão project.build.sourceEncoding nos projetos Maven. A lista de codificações disponíveis depende da sua JVM. | System encoding 
| **sonar.ws.timeout** | Tempo máximo para aguardar a resposta de uma chamada de serviço da Web (em segundos). Modificar esse valor do padrão é útil somente quando você estiver enfrentando tempos limite durante a análise enquanto aguarda o servidor responder a chamadas de serviço da Web. | 60 |


## Configurações do Projeto
| KEY | DESCRIPTION | DEFAULT | 
|-----|-------------|---------|
| **sonar.projectKey** | A chave exclusiva do projeto. Os caracteres permitidos são: letras, números, -, _,. e:, com pelo menos um não dígito. | Para projetos Maven, o padrão é <groupId>: <artifactId> |
| **sonar.projectName** | Nome do projeto que será exibido na interface da web. | 
| **sonar.projectVersion** | A versão do projeto. |  |
| **sonar.modules** |
| **sonar.projectBaseDir** | Use essa propriedade quando precisar que a análise ocorra em um diretório diferente daquele em que foi iniciada. POR EXEMPLO. a análise começa em _jenkins/jobs/myjob/workspace_, mas os arquivos a serem analisados ​​estão em _ftpdrop/cobol/project1_. O caminho pode ser relativo ou absoluto. Especifique não o diretório de origem, mas algum pai do diretório de origem. O valor especificado aqui se torna o novo "diretório de análise" e outros caminhos são especificados como se a análise estivesse iniciando no valor especificado em _sonar.projectBaseDir_. | |
| 




# Referencias
[1] Architecture and Integration : https://docs.sonarqube.org/6.7/ArchitectureandIntegration.html
[2] Analyzing Source Code : https://docs.sonarqube.org/6.7/AnalyzingSourceCode.html
[3] SonarScanner for Jenkins : https://docs.sonarqube.org/latest/analysis/scan/sonarscanner-for-jenkins/
[4] SonarQube Scanner for Jenkins : https://jenkins.io/doc/pipeline/steps/sonar/#sonarqube-scanner-for-jenkins
[5] SonarQube Scanner2.11 (plugin) : https://plugins.jenkins.io/sonar/
[5.1]SonarQube Scanner Plugin stored server authentication token in plain text  :  https://jenkins.io/security/advisory/2018-09-25/#SECURITY-1163
[6] Analysys Parameters : https://docs.sonarqube.org/latest/analysis/analysis-parameters/
[6.1] http://thiagocolen.blogspot.com/2014/09/sonarqube-forcar-analise.html
[6.2] Analyzing with SonarQube Scanner : https://docs.sonarqube.org/pages/viewpage.action?pageId=6963052




https://docs.sonarqube.org/latest/analysis/overview/
[https://jenkins.io/doc/pipeline/steps/sonar/#sonarqube-scanner-for-jenkins]
