# 📧 Detecção de Phishing em Emails

Este projeto implementa um modelo de **detecção de phishing em emails**, utilizando técnicas de **Processamento de Linguagem Natural (NLP)** e aprendizado de máquina leve, para permitir incorporação em ferramentas de uso direto pelo usuário, como extensões de navegador.

---

## 🎯 Definição do Problema

O objetivo deste projeto é **avaliar e identificar emails de phishing** de forma automática, utilizando apenas o texto contido no corpo do email.

**Premissas e hipóteses:**
- Emails de phishing podem ser detectados analisando o conteúdo textual.
- Modelos leves são suficientes para fornecer uma primeira linha de defesa ao usuário.
- A tradução para inglês permite que um único modelo generalize para múltiplos idiomas.

**Condições de seleção de dados:**
- Apenas o texto do email e a classificação (phishing/legítimo) foram utilizados.
- Arquivos anexos não são analisados.
- A escolha de analisar apenas o texto foi feita para garantir **simplicidade, baixo custo computacional e rapidez**.  
  ⚠️ Entretanto, outros metadados, como remetente ou links, podem ser úteis. Por exemplo:
  - Um remetente como `mercadolivre@gmail.com` provavelmente é um phishing, simulando um domínio legítimo.
  - Um link como `mercadolivre.vendasrapidas.com` também indica tentativa de simular um site legítimo.

**Descrição do dataset:**
- Dataset inicial: tabela CSV com duas colunas:
  - `Email Text`: texto do email
  - `Email Type`: classificação (`Safe Email` ou `Phishing Email`)
- A classificação alvo é **Phishing Email**.
- Limitação do TF-IDF: o modelo aprende apenas o vocabulário presente no dataset. Isso pode reduzir generalização caso o dataset seja muito específico.

---

## 📊 Análise Exploratória

### 1. Distribuição de classes
- Emails seguros (`Safe Email`): 60%
- Emails de phishing (`Phishing Email`): 40%
- Existe um **desbalanceamento moderado**, mas condizente com a realidade, onde a maioria dos emails são seguros.

### 2. Distribuição do tamanho dos emails
- Calculado o número de caracteres de cada email.
- Visualizações mostram que emails de phishing podem ter tamanhos variados, permitindo identificar **padrões relevantes para o modelo**.

✅ Resumo:
- Balanceamento de classes aceitável.
- Tamanho dos emails influencia a escolha das features de texto (TF-IDF e n-grams).

---

## ⚙️ Pré-processamento e Vetorização

1. **Limpeza do texto**
   - Conversão para minúsculas.
   - Remoção de quebras de linha e múltiplos espaços.
   - Remoção de pontuação e caracteres especiais.

2. **TF-IDF**
   - Converte cada email em vetor numérico baseado na importância das palavras.
   - Considera **unigramas e bigramas**.
   - Limita o vocabulário (`max_features=500`) para manter simplicidade e baixo consumo de memória.

3. **Separação treino/teste**
   - 80% treino, 20% teste.

4. **LazyClassifier**
   - Treina e avalia diversos modelos automaticamente.
   - Permite comparar algoritmos de forma rápida e científica.

---

## 📈 Avaliação com Recall da Classe Positiva

- Em detecção de phishing, **falsos negativos** (emails maliciosos não detectados) são críticos.
- **Recall da classe positiva** mede a capacidade de identificar corretamente emails de phishing.
- O modelo escolhido foi o que apresentou **maior Recall e menor tempo de execução** entre os modelos testados.

**Nota:** o "tempo" exibido pelo LazyClassifier indica **quanto demorou para treinar e avaliar cada modelo** nos dados de teste.

---

## 🧪 Avaliação em Datasets Adicionais

Para testar a **capacidade de generalização**, o modelo foi avaliado em datasets diferentes:

- Segundo dataset: emails distintos do dataset inicial, testando generalização.
- Terceiro dataset: proveniente de outra fonte, com colunas `Category` (legítimo/phishing) e `Message`.

⚠️ Observação: os resultados mostraram que o modelo **perde desempenho em datasets muito distintos**, indicando possível **overfitting**.

### Estratégia de múltiplos datasets
- Para reduzir overfitting, é possível juntar **5 datasets distintos**, dividir cada um em 80% treino e 20% teste, e treinar um modelo único.
- Isso aumenta a **abrangência do vocabulário** e melhora a generalização.

---

## 🌍 Predição em Múltiplos Idiomas

- O modelo foi treinado em **emails em inglês**.
- Para suportar outros idiomas, os textos são **traduzidos automaticamente para inglês** usando `deep_translator` (identifica o idioma original automaticamente).
- Essa abordagem permite que um **único modelo funcione para múltiplos idiomas**, sem necessidade de treinar modelos separados.

**Exemplo de idiomas testados:**
- 🇺🇸 Inglês
- 🇵🇹 Português
- 🇫🇷 Francês

✅ Observação:
- O modelo identifica corretamente phishing independentemente do idioma.
- A **probabilidade de pertencer à classe positiva** varia por idioma, mostrando o impacto da tradução.

---

## 🔹 Pipeline de Classificação

- Pipeline: `TF-IDF + LGBMClassifier` já treinado.
- Função `traduz_e_classifica`:
  1. Tradução do texto para inglês.
  2. Classificação pelo pipeline.
  3. Retorna predição e probabilidade de phishing.

- Função `predict(user_text)`:
  - Recebe texto do usuário.
  - Retorna resultado de phishing/safe e probabilidade.

---

## ✅ Conclusão

- O modelo leve com TF-IDF + LGBM mostrou **bom desempenho** no dataset original.
- Em datasets distintos, houve queda de desempenho, indicando **necessidade de maior diversidade de dados para treino**.
- Uso de tradução permite **aplicação em múltiplos idiomas**.
- A abordagem é simples, rápida e adequada para incorporação em ferramentas de uso direto pelo usuário.
