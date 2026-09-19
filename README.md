# GymAI — Chatbot Especialista em Academia e Musculação

## Sobre o projeto

O **GymAI** é um chatbot baseado em **Inteligência Artificial Generativa**, desenvolvido em Python e executado em um ambiente Jupyter/Google Colab.

O projeto utiliza um modelo da família **Gemini** para criar um assistente especializado em academia e musculação. O chatbot recebe um contexto previamente definido sobre a plataforma fictícia **GymAI** e utiliza esse conteúdo como base para responder às perguntas do usuário.

A proposta principal é demonstrar o uso de um **LLM (Large Language Model)** com contexto especializado, histórico de conversa, limitação de interações e encerramento automático após um número definido de perguntas.

> **Importante:** o chatbot foi configurado para trabalhar com o contexto fornecido no projeto. As informações apresentadas pelo assistente devem ser interpretadas como conteúdo demonstrativo da aplicação fictícia.

---

## Contexto da aplicação

A empresa fictícia **FitTech Soluções Inteligentes Ltda.** desenvolveu a plataforma **GymAI**, voltada para academias, personal trainers e praticantes de musculação.

A plataforma utiliza Inteligência Artificial Generativa para auxiliar na criação, acompanhamento e adaptação de treinos.

### Principais funcionalidades do GymAI

- **Criação Inteligente de Treinos:** geração de sugestões personalizadas considerando objetivos, experiência, frequência semanal, equipamentos disponíveis e preferências.
- **Adaptação de Treinos:** sugestão de alterações conforme evolução, disponibilidade de equipamentos ou dificuldades relatadas.
- **Acompanhamento de Desempenho:** registro de cargas, repetições, séries e frequência, permitindo acompanhar padrões de evolução.
- **Assistente Virtual de Musculação:** chatbot para dúvidas sobre exercícios, organização dos treinos, descanso, progressão de cargas e conceitos básicos de musculação.
- **Planejamento de Rotina:** organização dos treinos ao longo da semana considerando disponibilidade, grupos musculares e recuperação.

---

## Benefícios propostos

De acordo com o contexto utilizado pelo chatbot, a plataforma busca proporcionar:

- Maior personalização dos treinos;
- Redução do tempo necessário para elaboração e ajuste das rotinas;
- Maior acompanhamento da evolução dos alunos;
- Facilidade para adaptar treinos;
- Maior interação entre alunos e academia;
- Centralização das informações de treinamento e desempenho.

---

## Tecnologias utilizadas

O notebook utiliza principalmente:

- **Python**
- **Google Colab / Jupyter Notebook**
- **Google GenAI SDK**
- **Gemini**
- **Panel**
- **python-dotenv**
- **Variáveis de ambiente**
- **Google Colab User Data/Secrets**

### Fluxo simplificado

```text
Usuário
   │
   ▼
Interface Panel
   │
   ▼
Pergunta do usuário
   │
   ▼
Contexto especializado + histórico
   │
   ▼
Modelo Gemini
   │
   ▼
Resposta gerada
   │
   ▼
Interface do chatbot
```

---

## Configuração da API

A chave da API é obtida através do recurso `userdata` do Google Colab e utilizada para criar um arquivo `.env`.

O projeto verifica se o arquivo `.env` já existe antes de criá-lo. Dessa forma, uma execução posterior não sobrescreve automaticamente um `.env` existente.

A variável utilizada pelo projeto é:

```env
GEMINI_API_KEY=sua_chave_aqui
```

O arquivo `.env` é carregado através do `python-dotenv`:

```python
from dotenv import load_dotenv
import os

load_dotenv()

GOOGLE_API_KEY = os.getenv('GEMINI_API_KEY')
```

A chave é então utilizada para inicializar o cliente da API:

```python
from google import genai

client = genai.Client(api_key=GOOGLE_API_KEY)
```

### Segurança

O projeto também cria um `.gitignore` contendo:

```text
/.env
/.ipynb_checkpoints/
```

Isso evita que o arquivo `.env`, contendo a chave da API, seja versionado acidentalmente.

Também é criado um arquivo `env.example` como referência para a configuração:

```env
GEMINI_API_KEY=
```

**Nunca publique uma chave de API real no GitHub ou em outro repositório público.**

---

## Contexto especializado

O comportamento especializado do chatbot é definido pela variável:

```python
EXPERT_CONTEXT
```

Nela são armazenadas as informações sobre a empresa fictícia, a plataforma GymAI, suas funcionalidades, benefícios, requisitos técnicos, suporte e observações.

A cada pergunta, esse contexto é enviado ao modelo junto com a pergunta do usuário:

```text
Informações de contexto para o especialista
        +
Pergunta do usuário
        ↓
      Gemini
        ↓
     Resposta
```

Essa abordagem permite criar um chatbot especializado sem precisar treinar um modelo do zero.

---

## Histórico da conversa

O projeto mantém o histórico das mensagens na variável:

```python
context
```

Inicialmente, ela contém uma instrução que define o comportamento do bot:

```text
Você é um bot especialista em um tópico específico.
Suas respostas devem ser baseadas exclusivamente no contexto
fornecido a cada interação.
```

Durante a conversa, as perguntas e respostas são adicionadas ao histórico.

Isso permite que o modelo receba informações sobre as interações anteriores ao responder às próximas perguntas.

---

## Limite de perguntas

O chatbot possui um contador de perguntas:

```python
qa_counter = 0
```

e um limite definido por:

```python
MAX_QUESTIONS = 4
```

Portanto, na implementação atual, o usuário pode realizar **até 4 perguntas**.

Após atingir esse limite, o sistema:

1. Gera um resumo da conversa;
2. Exibe o resumo ao usuário;
3. Informa que a conversa foi encerrada;
4. Desabilita o campo de entrada;
5. Orienta que o kernel seja reiniciado para iniciar uma nova conversa.

> **Observação:** a descrição textual do notebook menciona um limite de 3 perguntas, mas o código implementado atualmente utiliza `MAX_QUESTIONS = 4`. O comportamento efetivo do programa, portanto, é de 4 perguntas.

---

## Geração das respostas

O projeto utiliza a função:

```python
get_completion_from_messages()
```

Ela transforma o histórico da aplicação para o formato esperado pela API do Google GenAI e chama o modelo:

```python
client.models.generate_content(...)
```

O modelo definido no código é:

```text
gemini-3.6-flash
```

A função recebe o histórico de mensagens e retorna o texto produzido pelo modelo.

---

## Interface do chatbot

A interface é construída utilizando a biblioteca **Panel**.

Os principais componentes são:

- `TextInput`: campo para digitação da pergunta;
- `Button`: botão para enviar a pergunta;
- `pn.Row`: organização das mensagens;
- `pn.pane.Markdown`: apresentação das mensagens;
- `pn.Column`: organização geral da interface.

A estrutura visual é essencialmente:

```text
┌─────────────────────────────────────────┐
│ Bot Especialista                        │
├─────────────────────────────────────────┤
│ [ Digite sua pergunta aqui...        ]  │
│                              [Enviar]   │
├─────────────────────────────────────────┤
│ Usuário:                                │
│ Pergunta enviada                        │
│                                         │
│ Assistente:                             │
│ Resposta gerada pelo Gemini             │
│                                         │
│ ...                                     │
└─────────────────────────────────────────┘
```

---

## Funcionamento da interação

Quando o usuário clica em **Enviar**, a função `collect_messages()` é executada.

O fluxo principal é:

1. Verificar se o limite de perguntas foi atingido;
2. Capturar a pergunta digitada;
3. Limpar o campo de entrada;
4. Exibir a pergunta na interface;
5. Combinar o histórico da conversa com o `EXPERT_CONTEXT`;
6. Enviar as informações ao modelo Gemini;
7. Receber a resposta;
8. Incrementar o contador de perguntas;
9. Armazenar pergunta e resposta no histórico;
10. Exibir a resposta na interface;
11. Se for a última pergunta, gerar o resumo e encerrar a conversa.

---

## Estrutura do notebook

O projeto está organizado em células com responsabilidades diferentes:

| Etapa | Responsabilidade |
|---|---|
| Instalação | Instala `jupyter_bokeh` e `python-dotenv` |
| API | Recupera e configura a chave do Gemini |
| Ambiente | Cria `.gitignore` e `env.example` |
| Contexto | Define as informações especializadas do GymAI |
| Cliente | Inicializa o cliente Google GenAI |
| Geração | Implementa funções para comunicação com o Gemini |
| Interface | Cria os componentes do chatbot usando Panel |
| Conversação | Controla perguntas, respostas e histórico |
| Encerramento | Gera resumo e bloqueia novas perguntas |

---

## Como executar

### 1. Abrir o notebook

Abra o arquivo:

```text
Fernando_Barros_19_09_IA.ipynb
```

em um ambiente compatível com Jupyter Notebook ou, preferencialmente, Google Colab.

### 2. Configurar a chave da API

No Google Colab, configure a chave utilizando o recurso de Secrets/User Data com o identificador:

```text
google
```

O notebook recuperará esse valor através de:

```python
userdata.get('google')
```

### 3. Executar as células

Execute as células do notebook em ordem.

O projeto irá:

- instalar as dependências;
- criar o `.env`, caso ele ainda não exista;
- carregar a chave da API;
- inicializar o cliente Gemini;
- configurar o contexto do GymAI;
- criar a interface do chatbot.

### 4. Utilizar o chatbot

Digite uma pergunta relacionada ao contexto do GymAI e clique em **Enviar**.

Exemplos:

```text
Quais são as principais funcionalidades do GymAI?
```

```text
Para quem o GymAI foi desenvolvido?
```

```text
Quais são os planos de suporte disponíveis?
```

```text
O GymAI substitui um profissional de educação física?
```

---

## Limitações

O projeto possui algumas limitações importantes:

- O conhecimento utilizado pelo chatbot é baseado no `EXPERT_CONTEXT` fornecido no código.
- O sistema não possui banco de dados persistente para armazenar conversas.
- O histórico existe apenas durante a execução atual do notebook.
- Ao reiniciar o kernel, o estado da conversa é perdido.
- O número de perguntas é limitado a 4 na implementação atual.
- O chatbot não substitui avaliação ou orientação profissional.
- As respostas são geradas por um modelo de IA e podem apresentar limitações ou imprecisões.
- O projeto não implementa autenticação de usuários.
- Não há, no código analisado, integração com sistemas reais de academias, fichas de treino ou dispositivos de monitoramento.

---

## Possíveis evoluções

O projeto pode ser expandido futuramente com:

- Banco de dados para armazenamento de alunos e treinos;
- Login e autenticação;
- Histórico permanente das conversas;
- Cadastro de exercícios;
- Registro de séries, repetições e cargas;
- Dashboard de evolução;
- Integração com aplicativos móveis;
- Diferentes perfis de usuário para alunos e profissionais;
- Interface para o profissional revisar as recomendações da IA;
- Sistema de geração de fichas de treino;
- Integração com APIs externas;
- Controle de permissões e acesso aos dados.

---

## Aviso

O GymAI apresentado neste projeto é uma **aplicação fictícia para fins demonstrativos e acadêmicos**.

As recomendações produzidas pelo chatbot não devem ser utilizadas como substituição de avaliação, diagnóstico ou orientação profissional.

---

## Autor

**Fernando Araujo Barros**

Projeto desenvolvido como demonstração de aplicação de **Inteligência Artificial Generativa, integração com LLM e desenvolvimento de chatbot em Python**.
