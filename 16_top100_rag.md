# Top 100 RAG (Retrieval-Augmented Generation) Interview Questions
> Focus: 8–15 Years | Non-FAANG | GenAI Companies, Healthcare AI, Enterprise AI
> Sources: Glassdoor, LinkedIn, Reddit, Medium, GenAI Engineer Interview Reports

---

## SECTION 1: RAG Fundamentals (Q1–Q20)

### Q1
- **Question:** What is RAG? Explain the full pipeline from document ingestion to answer generation.
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **Phase 1 — Indexing (offline):**
    1. Load documents (PDF, DOCX, HTML, etc.)
    2. Split into chunks (fixed-size, semantic, or hierarchical)
    3. Generate embeddings for each chunk
    4. Store chunks + embeddings in vector database
  - **Phase 2 — Retrieval + Generation (online):**
    1. User submits query
    2. Embed query using same embedding model
    3. Search vector DB for top-K similar chunks (ANN search)
    4. Rerank chunks (optional)
    5. Assemble prompt: system + retrieved context + user query
    6. LLM generates answer grounded in retrieved context
- **Why RAG over fine-tuning:** No model retraining; knowledge can be updated by updating documents; citation/source tracking; cheaper
- **Difficulty:** Medium

---

### Q2
- **Question:** What chunking strategies do you know? Which do you use for what type of document?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **Fixed-size chunking:** `chunk_size=512, overlap=50`; simple; may break sentences mid-thought
  - **Sentence/paragraph chunking:** respect natural boundaries; better coherence
  - **Semantic chunking:** embed sentences; merge until similarity drops; most coherent but expensive
  - **Hierarchical chunking (parent-child):** large parent chunks for context + small child chunks for precise retrieval
  - **Document-aware chunking:** chapter/section for books; table-aware for structured docs
- **Code:**
  ```python
  from langchain.text_splitter import RecursiveCharacterTextSplitter

  splitter = RecursiveCharacterTextSplitter(
      chunk_size=512,
      chunk_overlap=50,
      separators=["\n\n", "\n", ".", "!", "?", " ", ""]
  )
  chunks = splitter.split_text(document_text)
  ```
- **Follow-Ups:** How do you choose chunk size? What happens if chunks are too large vs too small?
- **Difficulty:** Medium

---

### Q3
- **Question:** How do you handle different document types (PDF, DOCX, HTML, tables) in a RAG pipeline?
- **Level:** 8–12 Yrs | **Company:** Healthcare GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  # PDF with layout preservation
  from unstructured.partition.pdf import partition_pdf
  elements = partition_pdf("document.pdf", strategy="hi_res",
                           infer_table_structure=True)

  # Separate tables from text
  tables = [e for e in elements if e.category == "Table"]
  text_blocks = [e for e in elements if e.category == "NarrativeText"]

  # Azure Document Intelligence (best for forms)
  from azure.ai.formrecognizer import DocumentAnalysisClient
  client = DocumentAnalysisClient(endpoint, credential)
  poller = client.begin_analyze_document("prebuilt-layout", document=pdf_bytes)
  result = poller.result()

  # DOCX
  from docx import Document
  doc = Document("file.docx")
  text = "\n".join(para.text for para in doc.paragraphs if para.text)
  ```
- **Answer Points:** Tables need special handling — convert to markdown or structured text before chunking; headers should be preserved as metadata
- **Difficulty:** Medium

---

### Q4
- **Question:** What metadata should you attach to each chunk? How does metadata filtering improve retrieval?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **Source metadata:** filename, URL, page number, section header, document type
  - **Content metadata:** created/updated date, author, department, language
  - **Domain metadata (healthcare):** document_type (policy/clinical/formulary), plan_code, effective_date, jurisdiction
  - **How filtering helps:** pre-filter by `plan_code == "PPO_2024"` before vector search → relevant results only; reduces noise
  ```python
  doc = {
      "content": chunk_text,
      "metadata": {
          "source": "formulary_2024.pdf",
          "page": 12,
          "section": "Section 3: Covered Medications",
          "plan_code": "PPO_2024",
          "effective_date": "2024-01-01",
          "doc_type": "formulary"
      }
  }
  ```
- **Difficulty:** Medium

---

### Q5
- **Question:** What is the difference between naive RAG and advanced RAG?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **Naive RAG:** embed query → retrieve top-K → stuff in prompt → LLM answers
  - **Advanced RAG (pre-retrieval):**
    - Query rewriting (HyDE, query expansion, step-back prompting)
    - Query routing (send to different indexes based on query type)
  - **Advanced RAG (retrieval):**
    - Hybrid search (dense + sparse/BM25)
    - Reranking (cross-encoder)
    - Multi-query retrieval
  - **Advanced RAG (post-retrieval):**
    - Context compression / relevance filtering
    - Answer grounding verification
    - Citation extraction
- **Difficulty:** Medium

---

### Q6
- **Question:** What is hybrid search in RAG? How does it combine dense and sparse retrieval?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **Dense retrieval:** embedding similarity; captures semantic meaning; misses exact keyword matches
  - **Sparse retrieval (BM25):** keyword-based TF-IDF scoring; exact matches; misses paraphrases
  - **Hybrid:** combine both scores — RRF (Reciprocal Rank Fusion) is most common
  ```python
  # Qdrant hybrid search
  results = client.query_points(
      collection_name="docs",
      prefetch=[
          models.Prefetch(query=dense_embedding, using="dense", limit=20),
          models.Prefetch(query=sparse_embedding, using="sparse", limit=20),
      ],
      query=models.FusionQuery(fusion=models.Fusion.RRF),
      limit=10
  )
  ```
- **When hybrid is better:** domain-specific terminology (drug names, procedure codes), proper nouns, IDs
- **Difficulty:** Hard

---

### Q7
- **Question:** What is reranking in RAG? How does a cross-encoder differ from a bi-encoder?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **Bi-encoder (retrieval):** encodes query and document separately; fast; approximate
  - **Cross-encoder (reranking):** processes query+document together; much more accurate; too slow for full corpus
  - **Workflow:** bi-encoder retrieves top-50 → cross-encoder reranks → take top-5 for LLM
  ```python
  from sentence_transformers import CrossEncoder

  reranker = CrossEncoder("cross-encoder/ms-marco-MiniLM-L-6-v2")

  pairs = [(query, chunk.text) for chunk in retrieved_chunks]
  scores = reranker.predict(pairs)

  reranked = sorted(zip(scores, retrieved_chunks), reverse=True)
  top_chunks = [chunk for _, chunk in reranked[:5]]
  ```
- **Difficulty:** Hard

---

### Q8
- **Question:** What is HyDE (Hypothetical Document Embeddings)? How does it improve retrieval?
- **Level:** 8–12 Yrs | **Company:** Senior GenAI | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  - Problem: query embedding often doesn't match document embedding well
  - HyDE: ask LLM to generate a hypothetical answer to the query; embed the hypothetical; use that embedding to retrieve
  ```python
  async def hyde_retrieve(query: str, k: int = 5) -> list[Chunk]:
      # Generate hypothetical document
      hypo_doc = await llm.complete(
          f"Write a detailed answer to: {query}\nAnswer:"
      )
      # Embed hypothetical answer (not query)
      hypo_embedding = await embed(hypo_doc)
      # Retrieve using hypothetical embedding
      return await vector_db.search(hypo_embedding, k=k)
  ```
- **When useful:** when queries are short/ambiguous; long-form technical questions
- **Difficulty:** Hard

---

### Q9
- **Question:** What is query rewriting in RAG? Name three techniques.
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **Multi-query:** generate 3 variations of the query; retrieve for each; merge results
  - **Step-back prompting:** abstract the query to a more general question first; retrieve for abstract; then specific
  - **Query decomposition:** break complex query into sub-questions; retrieve and answer each; synthesize
  - **Query expansion:** add synonyms, related terms, domain-specific alternatives
  ```python
  async def multi_query_retrieve(query: str) -> list[Chunk]:
      queries = await llm.complete(
          f"Generate 3 different versions of this query:\n{query}"
      )
      all_chunks = []
      for q in queries:
          chunks = await vector_db.search(await embed(q), k=5)
          all_chunks.extend(chunks)
      return deduplicate_by_id(all_chunks)
  ```
- **Difficulty:** Medium

---

### Q10
- **Question:** How do you evaluate a RAG pipeline? What metrics do you use?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **Retrieval metrics:**
    - Context Precision: how many retrieved chunks are actually relevant?
    - Context Recall: how many relevant chunks were retrieved?
    - MRR (Mean Reciprocal Rank): where is the first relevant chunk?
  - **Generation metrics:**
    - Faithfulness: is the answer grounded in retrieved context? (no hallucination)
    - Answer Relevance: does answer address the question?
    - Answer Correctness: is the answer factually correct?
  - **Tools:** RAGAS framework (`ragas` Python library)
  ```python
  from ragas import evaluate
  from ragas.metrics import faithfulness, answer_relevancy, context_precision, context_recall

  dataset = Dataset.from_dict({
      "question": questions,
      "contexts": retrieved_contexts,
      "answer": generated_answers,
      "ground_truth": reference_answers
  })
  results = evaluate(dataset, metrics=[faithfulness, answer_relevancy, context_precision, context_recall])
  ```
- **Difficulty:** Hard

---

### Q11–Q20 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 11 | What is `RAGAS`? How do you use it to evaluate your RAG pipeline automatically? | 8–12 Yrs | High | T1 |
| 12 | What is context compression? How do you remove irrelevant parts before sending to LLM? | 8–12 Yrs | Medium | T2 |
| 13 | How do you implement citation/source attribution in a RAG response? | 8–12 Yrs | High | T1 |
| 14 | What is the lost-in-the-middle problem in RAG? How do you mitigate it? | 8–12 Yrs | Medium | T2 |
| 15 | How do you handle conflicting information across retrieved chunks? | 8–12 Yrs | High | T1 |
| 16 | What is a RAG guard? How do you check if retrieved context actually answers the query? | 8–12 Yrs | Medium | T2 |
| 17 | How do you implement a fallback when retrieval returns no relevant results? | 8–12 Yrs | High | T1 |
| 18 | How do you choose between `top_k` vs a similarity score threshold for retrieval? | 8–12 Yrs | Medium | T2 |
| 19 | How do you keep vector store documents in sync with source document updates? | 8–12 Yrs | High | T1 |
| 20 | How do you handle multilingual documents in a RAG pipeline? | 8–12 Yrs | Medium | T2 |

---

## SECTION 2: Advanced RAG Patterns (Q21–Q55)

### Q21
- **Question:** What is hierarchical RAG (parent-document retrieval)? When is it better than flat chunking?
- **Level:** 8–12 Yrs | **Company:** Enterprise GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  # Small child chunks for precise retrieval
  # Large parent chunks for rich context in LLM
  from langchain.retrievers import ParentDocumentRetriever
  from langchain.storage import InMemoryStore

  parent_splitter = RecursiveCharacterTextSplitter(chunk_size=2000)
  child_splitter = RecursiveCharacterTextSplitter(chunk_size=400)

  store = InMemoryStore()  # or Redis/MongoDB for persistence
  retriever = ParentDocumentRetriever(
      vectorstore=vectorstore,
      docstore=store,
      child_splitter=child_splitter,
      parent_splitter=parent_splitter
  )
  # Retrieves small chunks but returns their parent chunks to LLM
  ```
- **When better:** long documents where small chunks miss context; questions requiring wider paragraph context
- **Difficulty:** Hard

---

### Q22
- **Question:** What is agentic RAG? How does an agent decide when to retrieve?
- **Level:** 12–15 Yrs | **Company:** GenAI Architect | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Traditional RAG: always retrieves for every query
  - Agentic RAG: agent uses retrieval as a tool — decides if/when/what to retrieve
  - Agent can: retrieve → read → decide to retrieve again with refined query → synthesize
  - Self-RAG: LLM generates its own retrieval decision tokens (`[Retrieve]`, `[No Retrieve]`)
  - Benefits: handles multi-hop questions; reduces unnecessary retrieval; more accurate on complex queries
- **Difficulty:** Hard

---

### Q23
- **Question:** What is GraphRAG? How does it differ from standard RAG?
- **Level:** 12–15 Yrs | **Company:** GenAI Architect | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  - Standard RAG: chunks + vector similarity; misses relationships between entities
  - GraphRAG (Microsoft): extract knowledge graph from documents; community detection; summarize communities; retrieve via graph traversal
  - Better for: questions requiring reasoning across multiple entities and their relationships
  - Tools: `microsoft/graphrag` Python library; LlamaIndex `PropertyGraphIndex`
  - Use when: complex multi-hop reasoning; relationship-heavy domains (healthcare: patient-condition-treatment-drug)
- **Difficulty:** Hard

---

### Q24
- **Question:** How do you implement a complete RAG pipeline in LangChain? Walk through the code.
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from langchain_openai import ChatOpenAI, OpenAIEmbeddings
  from langchain_community.vectorstores import Qdrant
  from langchain.chains import RetrievalQA
  from langchain.prompts import ChatPromptTemplate
  from langchain_core.runnables import RunnablePassthrough
  from langchain_core.output_parsers import StrOutputParser

  # Setup
  embeddings = OpenAIEmbeddings(model="text-embedding-3-small")
  vectorstore = Qdrant.from_existing_collection(
      embedding=embeddings, collection_name="docs", url=QDRANT_URL
  )
  retriever = vectorstore.as_retriever(search_kwargs={"k": 5})
  llm = ChatOpenAI(model="gpt-4o", temperature=0)

  # Prompt template
  prompt = ChatPromptTemplate.from_template("""
  Answer based ONLY on the following context. If the answer is not in the context, say "I don't have enough information."

  Context:
  {context}

  Question: {question}
  """)

  def format_docs(docs):
      return "\n\n".join(doc.page_content for doc in docs)

  # LCEL chain
  rag_chain = (
      {"context": retriever | format_docs, "question": RunnablePassthrough()}
      | prompt
      | llm
      | StrOutputParser()
  )

  answer = await rag_chain.ainvoke("What is the prior auth requirement for MRI?")
  ```
- **Difficulty:** Medium

---

### Q25
- **Question:** How do you implement RAG without LangChain (using raw Python)?
- **Level:** 8–12 Yrs | **Company:** Senior GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import openai
  from qdrant_client import QdrantClient

  async def rag_query(query: str) -> str:
      # 1. Embed query
      emb_response = await openai.AsyncOpenAI().embeddings.create(
          model="text-embedding-3-small", input=query
      )
      query_embedding = emb_response.data[0].embedding

      # 2. Retrieve
      qdrant = QdrantClient(url=QDRANT_URL)
      results = qdrant.search(
          collection_name="docs",
          query_vector=query_embedding,
          limit=5,
          score_threshold=0.7
      )
      context = "\n\n".join(r.payload["content"] for r in results)

      # 3. Generate
      response = await openai.AsyncOpenAI().chat.completions.create(
          model="gpt-4o",
          messages=[
              {"role": "system", "content": "Answer based on provided context only."},
              {"role": "user", "content": f"Context:\n{context}\n\nQuestion: {query}"}
          ]
      )
      return response.choices[0].message.content
  ```
- **Difficulty:** Medium

---

### Q26–Q55 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 26 | How do you implement multi-hop RAG for questions requiring multiple retrieval steps? | 12–15 Yrs | High | T1 |
| 27 | How do you implement a RAG pipeline for a multi-tenant application? | 12–15 Yrs | High | T1 |
| 28 | How do you handle tables and structured data in a RAG pipeline? | 8–12 Yrs | High | T1 |
| 29 | How do you implement a RAG pipeline for code documentation (code search)? | 8–12 Yrs | Medium | T2 |
| 30 | What is self-RAG? How does the model control its own retrieval process? | 12–15 Yrs | Medium | T2 |
| 31 | How do you implement a confidence-gated RAG (only use context if confidence > threshold)? | 12–15 Yrs | Medium | T2 |
| 32 | How do you implement RAG with streaming responses? | 8–12 Yrs | High | T1 |
| 33 | How do you implement document versioning in a RAG system? | 8–12 Yrs | Medium | T2 |
| 34 | How do you handle very long documents (100+ pages) in RAG? | 8–12 Yrs | High | T1 |
| 35 | What is `LlamaIndex`? How does it compare to LangChain for RAG? | 8–12 Yrs | Medium | T2 |
| 36 | How do you implement query routing to multiple specialized RAG indexes? | 8–12 Yrs | High | T1 |
| 37 | How do you implement a RAG system that cites exact page numbers and paragraphs? | 8–12 Yrs | High | T1 |
| 38 | How do you implement a RAG chatbot with session memory? | 8–12 Yrs | High | T1 |
| 39 | What is contextual compression in RAG? How does LLMChainExtractor work? | 8–12 Yrs | Medium | T2 |
| 40 | How do you implement chunking that respects section headers? | 8–12 Yrs | High | T1 |
| 41 | How do you implement a RAG pipeline for medical policy documents? | 12–15 Yrs | High | T1 |
| 42 | How do you handle OCR errors in scanned document RAG? | 8–12 Yrs | Medium | T2 |
| 43 | How do you implement a RAG pipeline with answer verification against source? | 8–12 Yrs | High | T1 |
| 44 | What is FLARE (Forward-Looking Active Retrieval)? How does it work? | 12–15 Yrs | Low | T3 |
| 45 | How do you implement late chunking (chunk after embedding full document)? | 12–15 Yrs | Low | T3 |
| 46 | How do you implement a RAG system that learns from user feedback? | 12–15 Yrs | Medium | T2 |
| 47 | How do you optimize RAG latency for real-time user-facing applications? | 8–12 Yrs | High | T1 |
| 48 | How do you implement pre-filtering by metadata before vector search? | 8–12 Yrs | High | T1 |
| 49 | What is Reciprocal Rank Fusion (RRF)? How does it combine multiple ranked lists? | 8–12 Yrs | Medium | T2 |
| 50 | How do you implement a RAG pipeline for code understanding and generation? | 8–12 Yrs | Medium | T2 |
| 51 | How do you handle deleted/updated documents in an existing vector store? | 8–12 Yrs | High | T1 |
| 52 | How do you implement namespace-based isolation in Qdrant for multi-tenant RAG? | 12–15 Yrs | High | T1 |
| 53 | What is ColPali? How does it handle multi-modal documents (PDF pages as images)? | 12–15 Yrs | Low | T3 |
| 54 | How do you implement adaptive RAG that selects strategy based on query complexity? | 12–15 Yrs | Medium | T2 |
| 55 | How do you implement a RAG pipeline for regulatory compliance documents? | 12–15 Yrs | High | T1 |

---

## SECTION 3: RAG in Production (Q56–Q100)

### Q56
- **Question:** How do you monitor a RAG pipeline in production?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **Retrieval metrics:** avg similarity score, retrieval latency, empty retrieval rate
  - **Generation metrics:** LLM latency, token usage, hallucination rate (LLM-as-judge)
  - **Business metrics:** user thumbs up/down, escalation rate, fallback rate
  - **Tools:** Langfuse, LangSmith, OpenTelemetry custom spans
  ```python
  # Langfuse tracing
  from langfuse import Langfuse
  langfuse = Langfuse()

  trace = langfuse.trace(name="rag_query", user_id=user_id)
  retrieval_span = trace.span(name="retrieval", input={"query": query})
  # ... retrieve ...
  retrieval_span.end(output={"chunks": len(chunks), "top_score": top_score})
  ```
- **Difficulty:** Medium

---

### Q57
- **Question:** How do you handle RAG pipeline failures gracefully in production?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Vector DB unavailable → fallback to keyword search or cached results
  - LLM API timeout → retry with exponential backoff; fallback to simpler model
  - No relevant chunks retrieved → return "I don't have enough information" + escalate
  - Context too long → truncate/compress before sending to LLM
  - LLM returns malformed output → retry with corrective prompt; use `Instructor`/Pydantic validation
- **Difficulty:** Medium

---

### Q58–Q100 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 58 | How do you implement a RAG pipeline with cost controls (max tokens, max cost/query)? | 8–12 Yrs | High | T1 |
| 59 | How do you A/B test two RAG configurations in production? | 12–15 Yrs | High | T1 |
| 60 | How do you build a RAG evaluation dataset from scratch? | 8–12 Yrs | High | T1 |
| 61 | How do you use LLM-as-a-judge to evaluate RAG faithfulness automatically? | 8–12 Yrs | High | T1 |
| 62 | How do you implement a RAG pipeline with access controls (user can only see their docs)? | 12–15 Yrs | High | T1 |
| 63 | How do you implement a HIPAA-compliant RAG system for clinical documentation? | 12–15 Yrs | High | T1 |
| 64 | How do you handle PII in documents before adding to a RAG vector store? | 12–15 Yrs | High | T1 |
| 65 | How do you scale a RAG pipeline to 10M documents? | 12–15 Yrs | High | T1 |
| 66 | How do you implement a real-time RAG pipeline where documents update continuously? | 12–15 Yrs | Medium | T2 |
| 67 | How do you measure RAG pipeline cost and optimize it? | 8–12 Yrs | High | T1 |
| 68 | How do you implement a RAG pipeline for a healthcare formulary chatbot? | 12–15 Yrs | High | T1 |
| 69 | How do you implement a RAG pipeline for member eligibility questions? | 12–15 Yrs | High | T1 |
| 70 | How do you debug a RAG pipeline that's returning incorrect answers? | 8–12 Yrs | High | T1 |
| 71 | What is chunk poisoning? How do you prevent malicious content in your vector store? | 12–15 Yrs | Medium | T2 |
| 72 | How do you implement offline evaluation for a RAG system before production? | 8–12 Yrs | High | T1 |
| 73 | How do you implement a RAG pipeline using Azure AI Search as the vector store? | 8–12 Yrs | High | T1 |
| 74 | How do you implement hybrid search in Azure AI Search with Python? | 8–12 Yrs | High | T1 |
| 75 | How do you implement a RAG pipeline with Haystack? | 8–12 Yrs | Low | T3 |
| 76 | How do you implement a RAG pipeline with LlamaIndex? | 8–12 Yrs | Medium | T2 |
| 77 | How do you implement document-level access control in Qdrant? | 12–15 Yrs | High | T1 |
| 78 | How do you implement incremental indexing when new documents arrive? | 8–12 Yrs | High | T1 |
| 79 | How do you handle duplicate documents in the vector store? | 8–12 Yrs | Medium | T2 |
| 80 | How do you implement a RAG pipeline for legal/compliance documents? | 12–15 Yrs | High | T1 |
| 81 | How do you implement conversation-aware RAG with chat history? | 8–12 Yrs | High | T1 |
| 82 | How do you use `Unstructured.io` for pre-processing complex documents for RAG? | 8–12 Yrs | Medium | T2 |
| 83 | How do you implement a RAG pipeline for audio transcripts? | 8–12 Yrs | Medium | T2 |
| 84 | How do you implement a RAG system that uses both local LLM and cloud LLM? | 8–12 Yrs | Medium | T2 |
| 85 | What is speculative RAG? How does it use a small model for drafting? | 12–15 Yrs | Low | T3 |
| 86 | How do you generate synthetic RAG evaluation data using GPT-4? | 8–12 Yrs | High | T1 |
| 87 | How do you implement a RAG pipeline that handles follow-up questions in a conversation? | 8–12 Yrs | High | T1 |
| 88 | What are the failure modes of RAG? How do you classify and monitor them? | 12–15 Yrs | High | T1 |
| 89 | How do you implement topic-based routing to specialized RAG agents? | 12–15 Yrs | High | T1 |
| 90 | How do you implement a feedback loop to improve RAG retrieval over time? | 12–15 Yrs | Medium | T2 |
| 91 | How do you implement batch processing for bulk RAG queries? | 8–12 Yrs | High | T1 |
| 92 | How do you implement a RAG pipeline for structured + unstructured hybrid data? | 12–15 Yrs | High | T1 |
| 93 | How do you implement real-time streaming RAG with token-by-token output? | 8–12 Yrs | High | T1 |
| 94 | How do you implement watermarking or fingerprinting of RAG-generated content? | 12–15 Yrs | Low | T3 |
| 95 | How do you implement a RAG pipeline for internal enterprise knowledge base? | 12–15 Yrs | High | T1 |
| 96 | How do you build a ground truth evaluation set for a RAG system from scratch? | 8–12 Yrs | High | T1 |
| 97 | How do you implement a RAG pipeline that switches between retrieval and reasoning? | 12–15 Yrs | Medium | T2 |
| 98 | How do you implement a RAG system for a 1M-document medical literature database? | 12–15 Yrs | High | T1 |
| 99 | How do you implement responsible AI practices in a healthcare RAG system? | 12–15 Yrs | High | T1 |
| 100 | Design a production-grade RAG system for healthcare prior authorization: document ingestion, hybrid search, reranking, LLM generation, citation, compliance, monitoring, and multi-tenant isolation. | 12–15 Yrs | High | T1 |
