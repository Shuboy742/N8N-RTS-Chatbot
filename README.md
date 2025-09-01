# PMC RTS RAG WhatsApp Chatbot (n8n)

A Retrieval-Augmented Generation (RAG) chatbot for the PMC RTS portal that answers questions about departments, services, and required documents. Built with n8n automation, Google Gemini 2.5 Flash, Pinecone vector database, and Hugging Face embeddings. Integrated with WhatsApp via Meta for Business.

Related link: PMC RTS Portal — https://services.pmc.gov.in/login

## Features

1. RAG pipeline using Pinecone for vector search over PMC RTS content
2. Hugging Face `sentence-transformers/all-mpnet-base-v2` embeddings
3. Google Gemini 2.5 Flash as the LLM for responses
4. n8n AI Agent with strict grounding to vector search results
5. Simple session memory of the last 15 messages per WhatsApp user
6. End-to-end WhatsApp integration using Meta for Business APIs
7. English/Marathi support with language mirroring

## Repository Contents

1. `S- RAG chatbot RTS.json`: n8n workflow export for the complete chatbot
2. `README.md`: This documentation

## Architecture Overview

1. WhatsApp user sends a message → n8n WhatsApp Trigger node receives the event
2. AI Agent queries Pinecone via the vector-store tool using MPNet embeddings
3. Gemini 2.5 Flash generates an answer strictly grounded in retrieved documents
4. Simple memory window (15 turns) keeps the conversation context per user
5. Response is sent back to the user via the WhatsApp Send node

## n8n Workflow Highlights

1. WhatsApp Trigger → `n8n-nodes-base.whatsAppTrigger`
2. AI Agent → `@n8n/n8n-nodes-langchain.agent`
3. Vector Store (Pinecone) → `@n8n/n8n-nodes-langchain.vectorStorePinecone`
4. Embeddings → `@n8n/n8n-nodes-langchain.embeddingsHuggingFaceInference`
5. LLM → `@n8n/n8n-nodes-langchain.lmChatGoogleGemini`
6. Simple Memory → `@n8n/n8n-nodes-langchain.memoryBufferWindow` with 15-message context window
7. WhatsApp Send → `n8n-nodes-base.whatsApp`

## Prerequisites

1. n8n (self-hosted or cloud) with access to install and configure credentials
2. Pinecone account and index (recommended name: `pmc-rts-services`, dimension: 768)
3. Hugging Face Inference API key
4. Google Gemini API key (for Gemini 2.5 Flash)
5. Meta for Business WhatsApp credentials: Phone Number ID, App ID, App Secret, Access Token, Verify Token
6. PMC RTS documents/data embedded into Pinecone

## Setup Instructions

1. Clone this repository
   - `git clone https://github.com/Shuboy742/N8N-RTS-Chatbot.git`
   - `cd N8N-RTS-Chatbot`
2. Import the n8n workflow
   - In n8n, go to Workflows → Import from File
   - Select `S- RAG chatbot RTS.json`
3. Configure credentials in n8n
   - Pinecone API credentials
   - Hugging Face Inference API key
   - Google Gemini API key
   - WhatsApp Business API (Phone Number ID + Access Token)
4. Prepare Pinecone index
   - Name: `pmc-rts-services`
   - Dimension: 768 (for `all-mpnet-base-v2`)
   - Metric: cosine or dot-product (cosine recommended)
5. Ingest data into Pinecone
   - Generate embeddings with `sentence-transformers/all-mpnet-base-v2`
   - Upsert vectors to `pmc-rts-services`
6. WhatsApp webhook configuration
   - Set the webhook URL provided by the n8n WhatsApp Trigger node
   - Verify the webhook using your Verify Token
   - Subscribe to message events
7. Activate the workflow
   - Enable the workflow in n8n once all credentials are valid

## Environment Variables (suggested)

1. `PINECONE_API_KEY`
2. `PINECONE_ENVIRONMENT` or `PINECONE_HOST`
3. `HUGGINGFACE_API_KEY`
4. `GOOGLE_API_KEY` (Gemini)
5. `WHATSAPP_PHONE_NUMBER_ID`
6. `WHATSAPP_ACCESS_TOKEN`
7. `WHATSAPP_VERIFY_TOKEN`

These are typically stored in n8n Credentials. Do not commit secrets.

## Usage

1. Send a WhatsApp message to the connected Business number
2. The bot mirrors language (English/Marathi) and answers strictly from Pinecone
3. If no relevant data is found, the bot asks to rephrase or narrow the query

## Grounding Policy (AI Agent)

1. Answer only from Pinecone search results
2. Do not invent departments, services, or documents
3. Use numbered lists or concise sentences for required documents
4. Respect user language (English or Marathi)

## Testing

1. Ask: "Which documents are needed for a caste certificate?"
2. Expect a numbered list of documents from the vector results
3. Ask in Marathi: "जात प्रमाणपत्रासाठी कोणते कागदपत्र लागतात?"
4. Expect Marathi answer with grounded documents
5. Send follow-up within 15 messages to test memory persistence

## Security & Compliance

1. Never expose API keys or access tokens
2. Restrict n8n access and configure HTTPS for webhooks
3. Sanitize logs and avoid storing PII unnecessarily

## Troubleshooting

1. Empty or generic replies
   - Check Pinecone index, embeddings dimension, and data coverage
2. Credential errors
   - Revalidate n8n credentials for Pinecone/Hugging Face/Gemini/WhatsApp
3. WhatsApp webhooks not firing
   - Verify webhook URL, Verify Token, and event subscriptions


## Links

1. PMC RTS Portal: https://services.pmc.gov.in/login
2. Repository: https://github.com/Shuboy742/N8N-RTS-Chatbot

## Photos
