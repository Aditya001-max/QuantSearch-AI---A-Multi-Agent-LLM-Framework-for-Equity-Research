# AlphaAgent

An implementation of the **AlphaAgent** paper published by BlackRock, adapted using **CrewAI** for modular, extensible, and interpretable multi-agent financial decision-making.

This framework integrates **Semantic RAG**, **fundamental analysis**, **valuation metrics**, **technical indicators**, **news sentiment**, and a **structured multi-agent debate** to produce a final BUY/SELL recommendation with a confidence score.

📄 Paper: https://arxiv.org/abs/2508.11152

---

# 🚀 How This Implementation Extends the Original AlphaAgents Paper

This repository substantially improves upon the AlphaAgents architecture described in the BlackRock paper. While the paper introduces a 3-agent system (Fundamental, Sentiment, Valuation) with a simple debate loop, this implementation adds **richer tools, more agents, stricter debate mechanics, superior RAG, technical indicators, and a fully automated consensus engine**.

### ✅ 1. Expanded Agent Architecture (5 Analysts vs. 3 in Paper)
The original paper only defines:
- Fundamental Agent  
- Valuation Agent  
- Sentiment Agent  

This implementation adds:

#### **➤ Technical Analyst (New)**
Not implemented in the paper but mentioned as future work.  
Provides:
- SMA/EMA indicators  
- RSI  
- MACD  
- Trend classification  
- Composite trading signals  

#### **➤ Moderator (New)**
Replaces AutoGen group assistant.  
Handles:
- Multi-turn debate  
- Ensures equal participation  
- Manages argument exchange and challenges  

#### **➤ Conclusion Agent (New)**
Not in the paper.  
Computes:
- Weighted confidence score  
- Final BUY/SELL  
- Outputs strict 2-word final signal `CS787 <score>`

---

### ✅ 2. Stronger Debate System: Multi-Turn, Structured, Weighted Voting  
The paper uses AutoGen's generic group chat; this system:

- Forces **each analyst to speak at least twice**
- Includes **argument–response–challenge loops**
- Uses **confidence scoring** from each agent
- Computes a **weighted average** of agent confidence
- Ensures non-zero, skewed outputs  
  (The paper has no quantitative debate scoring.)

---

### ✅ 3. More Powerful and Realistic RAG System  
The paper uses a simple section-based financial RAG.  
This repo upgrades it to:

- **SemanticChunker** for meaning-preserving splits  
- **MMR-based retrieval** for diverse, high-quality context  
- **Persistent ChromaDB** for multi-stock analysis  
- Support for **any financial PDF**, not only 10-K/10-Q  

These improvements reduce hallucinations and improve factual recall.

---

### ✅ 4. Full Technical Analysis Pipeline (Missing in Paper)
The paper mentions technical agents as *future possibility*.  
This repo implements:

- SMA20/SMA50  
- EMA20/EMA50  
- RSI (14-day)  
- MACD  
- Bullish/bearish trend detection  
- Final technical BUY/SELL + confidence  

This significantly expands AlphaAgents into multi-horizon analysis.

---

### ✅ 5. Unified Confidence Framework Across All Agents  
Each agent outputs:
- BUY/SELL  
- Confidence score ∈ (-1, 1), never 0  

The Conclusion Agent:
- Computes a **weighted** consensus  
- Applies **skew** to avoid zero outputs  
- Produces final **CS787 <score>** signature  

The original paper has no unified numerical scoring mechanism.

---

### ✅ 6. Replacing AutoGen with CrewAI  
The paper’s system relies on AutoGen.  
This repo uses **CrewAI**, which adds:

- Cleaner agent definitions  
- Declarative tasks  
- Better tool integration  
- Easier expansion  
- Production-ready orchestration  

This makes the architecture **more modular, maintainable, and realistic**.

---

# 🎯 Features

### 🧠 Multi-Agent System (5 Analysts + 2 Meta-Agents)
AlphaAgent includes five specialized analysts:

1. **Fundamental Analyst**  
2. **Valuation Analyst**  
3. **Sentiment Analyst**  
4. **Technical Analyst**  
5. **Moderator**  
6. **Conclusion Agent**

Each analyst provides a BUY or SELL decision **with a non-zero confidence score**.

---

### 📄 Semantic RAG
- PDFs loaded via DirectoryLoader + PyPDFLoader  
- Meaning-preserving splits using **SemanticChunker**  
- Embeddings: OpenAI text-embedding-ada-002  
- Persistent ChromaDB store  
- MMR retrieval  

---

### 📈 Technical Tools
- SMA20, SMA50  
- EMA20, EMA50  
- RSI (14d)  
- MACD  
- Trend detection  
- Final technical bias  

---

### 📰 News Tools
- `findnewsTool` – fetches latest news  
- `getNewsBodyTool` – reads news files  

---

### 📉 Valuation Tools
- Annualized returns  
- Annualized volatility  

---

### 📊 Fundamental Tools
- Balance sheet summarizer (GPT-4o-mini)  
- RAG-based fundamental extraction  

---

## 🛠 Tools (from crew.py)

### 🔍 RAG
`CustomRagTool`  
- Semantic-chunked retrieval  
- Used primarily by Fundamental Analyst  

### 📰 News  
`findnewsTool`, `getNewsBodyTool`

### 📉 Valuation  
`getAnnualisedVolatilityTool`  
`getAnnualisedReturnTool`

### 📊 Fundamental  
`fundamental_analysis_tool`

### 📈 Technical  
`getMovingAveragesTool`  
`getRSITool`  
`getMACDTool`  
`getTechnicalSignalTool`

---

## 🧠 Agents (from agents.yaml)

| Agent | Purpose |
|-------|---------|
| Fundamental Analyst | RAG + balance sheet analysis |
| Sentiment Analyst | News sentiment & market tone |
| Valuation Analyst | Returns + volatility metrics |
| Technical Analyst | SMA/EMA/RSI/MACD trend analysis |
| Moderator | Manages structured debate |
| Conclusion Agent | Computes final consensus |

---

## 🚦 Task Workflow (from tasks.yaml)

Sequential execution:

1. **fundamental_task**  
2. **sentiment_task**  
3. **valuation_task**  
4. **technical_task**  
5. **investment_debate_task**  
6. **investment_conclusion_task**  

The Conclusion Agent outputs exactly:

CS787 '<real-number>'

Where `<real-number>` ∈ **(-1, 1)** and is **never 0**.

