# IRBot – Assistente de Imposto de Renda com IA + WhatsApp

O IRBot é uma solução inteligente desenvolvida em Python que automatiza o processo de declaração do imposto de renda. Utiliza integração com **WhatsApp**, **IA conversacional (OpenAI)**, **geração de PDF** e **envio automático para a Receita Federal**, com validação e aceite digital do cliente.

### Principais Funcionalidades
- 📲 Coleta de dados via WhatsApp (Twilio API)
- 🤖 Conversa com IA para preencher a declaração
- 📄 Geração de PDF para validação do cliente
- ✅ Aceite digital com assinatura via hash
- 📤 Envio automático da declaração à Receita

### Tecnologias
- Python · FastAPI · OpenAI · Twilio · WeasyPrint · MongoDB · Gov.br OAuth

### Objetivo
Facilitar a vida do contribuinte e do contador, trazendo agilidade, segurança e praticidade com uso de inteligência artificial e automação.

---

## 🚀 Rodando o projeto localmente

### 1. Clone o repositório
```bash
git clone https://github.com/seuusuario/irbot.git
cd irbot
git clone https://github.com/seuusuario/irbot.git
cd irbot

codigo
from fastapi import APIRouter
from app.services.whatsapp_service import send_whatsapp_message
from app.services.ai_service import ask_ai
from app.services.pdf_service import generate_pdf
from app.services.aceite_service import gerar_hash_digital
import uuid

router = APIRouter()

@router.post("/processar-ir/")
def processar_ir(nome: str, cpf: str, rendimentos: float, deducoes: float, whatsapp: str):
    dados = {
        "nome": nome,
        "cpf": cpf,
        "rendimentos": rendimentos,
        "deducoes": deducoes
    }

    # 1. Gerar PDF
    pdf_path = f"/tmp/{uuid.uuid4()}.pdf"
    generate_pdf(dados, pdf_path)

    # 2. Gerar mensagem e hash
    hash_aceite = gerar_hash_digital(cpf, str(dados))
    mensagem = f"""
Olá {nome}, sua declaração de IR está pronta.
Para autorizar o envio, clique aqui: https://seusite.com/aceitar/{hash_aceite}
(ou envie esse código: {hash_aceite})
"""

    # 3. Enviar via WhatsApp
    send_whatsapp_message(whatsapp, mensagem)

    return {"status": "Declaração gerada e enviada para aceite no WhatsApp"}


1️⃣ Integração com WhatsApp (usando Twilio)

📁 app/services/whatsapp_service.py

from twilio.rest import Client
import os
from dotenv import load_dotenv

load_dotenv()

TWILIO_SID = os.getenv("TWILIO_ACCOUNT_SID")
TWILIO_AUTH = os.getenv("TWILIO_AUTH_TOKEN")
TWILIO_NUMBER = os.getenv("TWILIO_WHATSAPP_NUMBER")  # exemplo: 'whatsapp:+14155238886'

client = Client(TWILIO_SID, TWILIO_AUTH)

def send_whatsapp_message(to: str, message: str):
    message = client.messages.create(
        from_=TWILIO_NUMBER,
        body=message,
        to=f'whatsapp:{to}'
    )
    return message.sid
