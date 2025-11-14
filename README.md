# Global-Solution-2025-Edge-Computing

Integrantes: Gustavo Oliveira Barroso RM: 565705 / Nicolas Santana Gara RM: 561461 / Gustavo garcia Silva RM: 562078

# 📌 Sistema IoT de Monitoramento de Estresse em Home Office
Projeto desenvolvido para o **Global Solutions 2025 – O Futuro do Trabalho**

---

## 🧠 Descrição do Problema

O avanço do trabalho remoto trouxe inúmeros benefícios, mas também novos desafios.  
Profissionais em **home office** frequentemente enfrentam:

- aumento de estresse,  
- falta de pausas adequadas,  
- cansaço mental,  
- queda de produtividade.

Sem ferramentas de monitoramento, fica difícil identificar momentos críticos e garantir bem-estar no ambiente de trabalho.

---

## 🚀 Solução Proposta

Este projeto apresenta um sistema IoT utilizando **ESP32**, simulado no **Wokwi**, que monitora o nível de estresse de profissionais em home office.  
A solução:

✔ Lê um valor de estresse por meio de um sensor simulado (potenciômetro)  
✔ Envia esses dados para plataformas externas via HTTP  
✔ Ativa automaticamente um **LED de alerta** quando o estresse atingir níveis elevados  
✔ Envia dados simultaneamente para:
- **ThingSpeak** → armazenamento e gráfico  
- **Azure VM** → servidor próprio para processamento

Este sistema demonstra como a IoT pode contribuir para a saúde, automação e bem-estar no Futuro do Trabalho.

---

## 🧩 Arquitetura do Sistema

- **ESP32 (Wokwi)**
- **Potenciômetro** → simula nível de estresse (0–100)
- **LED vermelho** → acende quando ultrapassa o limite configurado
- **ThingSpeak** → registro de dados via HTTP
- **Azure VM (Linux)** → recebe dados via HTTP POST
- **Servidor Python na VM**:
  - Exibe valores recebidos
  - Confirma comunicação com o ESP32

---

## 🔗 Link para o Projeto no Wokwi

https://wokwi.com/projects/447470438647202817

## 🔗 Link para o ThingSpeak

https://thingspeak.mathworks.com/channels/3161516

---

## 💡 Funcionamento do Sistema

1. O sensor simulado gera um valor entre **0 e 100**.  
2. O ESP32 interpreta esse valor como nível de estresse.  
3. Os dados são enviados para:
   - **ThingSpeak** (HTTP GET)
   - **Azure VM** (HTTP POST)
4. Quando o valor ultrapassa o limite configurado (ex.: 70):
   - O **LED acende**
   - O sistema indica que o usuário deve fazer uma pausa
5. A VM exibe no terminal todas as leituras recebidas em tempo real.

---

## 🛠 Dependências

### No ESP32 (Wokwi)
- Biblioteca WiFi.h  
- Biblioteca HTTPClient.h  
- Hardware simulado:
  - ESP32
  - Potenciômetro
  - LED + resistor

### Na VM do Azure
- Ubuntu Server  
- Python 3 instalado  
- Script de servidor HTTP:

python
<!-- from http.server import BaseHTTPRequestHandler, HTTPServer

class Handler(BaseHTTPRequestHandler):
    def do_POST(self):
        content_length = int(self.headers.get('Content-Length', 0))
        data = self.rfile.read(content_length)
        print("Recebido:", data.decode())
        
        self.send_response(200)
        self.end_headers()
        self.wfile.write(b"OK")

server = HTTPServer(("0.0.0.0", 80), Handler)
print("Servidor HTTP rodando na porta 80...")
server.serve_forever() -->

---

🖼 Imagens do Circuito

<img width="1359" height="598" alt="Captura de tela 2025-11-14 133357" src="https://github.com/user-attachments/assets/afeabfac-f9e0-4a7c-acb8-cc7dcbd71d85" />

<img width="1359" height="598" alt="Captura de tela 2025-11-14 133430" src="https://github.com/user-attachments/assets/f4d3a783-9966-4e6b-bed6-5436a6d139d7" />

<img width="1359" height="598" alt="Captura de tela 2025-11-14 133916" src="https://github.com/user-attachments/assets/39e08f4d-03a7-44c2-9a33-3a2c5baa3d75" />

<img width="920" height="436" alt="Captura de tela 2025-11-14 133943" src="https://github.com/user-attachments/assets/3b4b8222-3b5b-4fc5-be0c-e7afa2ed735a" />

<img width="1070" height="583" alt="Captura de tela 2025-11-14 134014" src="https://github.com/user-attachments/assets/85fe412a-9cb3-4e87-a896-45bb7a95c371" />

---

🧪 Como Testar o Projeto
1️⃣ Rodar o projeto no Wokwi

Ajuste o potenciômetro para simular estresse

Observe o LED acendendo no valor crítico

2️⃣ ThingSpeak

Acesse seu canal

Veja o gráfico atualizando automaticamente

3️⃣ Azure VM

Execute na VM:

sudo python3 server.py


Verifique os valores chegando:

Recebido: stress=42
Recebido: stress=87

---

🧭 Impacto e Relevância

Este projeto mostra como tecnologias como IoT, automação e sensores inteligentes podem:

melhorar a saúde mental no trabalho remoto,

prevenir esgotamento,

oferecer suporte inteligente ao trabalhador,

criar ambientes de trabalho mais saudáveis e eficientes.

É uma aplicação prática do tema O Futuro do Trabalho, unindo automação, bem-estar e tecnologias emergentes.

---

✔ Conclusão

O sistema demonstra como uma solução simples, utilizando ESP32, HTTP, nuvem e sensores, pode apoiar profissionais em home office, promovendo um futuro mais humano, seguro e produtivo.

---

Código do Wokwi:

#include <WiFi.h>
#include <HTTPClient.h>

// ======== CONFIGURAÇÕES ========
const char* WIFI_SSID = "Wokwi-GUEST";      // Wi-Fi virtual do Wokwi
const char* WIFI_PASS = "";                 // sem senha
const char* THINGSPEAK_API_KEY = "5IMCRSXGPQRX75BB";  // sua chave de escrita
const char* THINGSPEAK_HOST = "http://api.thingspeak.com";

// ======== VM AZURE ========
const char* AZURE_VM_IP = "68.211.112.105";  // IP público da VM
const int AZURE_PORT = 80;                   // Porta HTTP padrão

// ======== PINOS ========
const int PIN_POT = 34;  // entrada analógica (potenciômetro)
const int PIN_LED = 2;   // saída digital (LED indicador)

// ======== PARÂMETROS ========
const int LIMIAR_STRESS = 65;
const int SAMPLE_INTERVAL_MS = 500;
const int ENVIO_INTERVALO_MS = 15000;

// ======== VARIÁVEIS ========
unsigned long ultimoEnvio = 0;
unsigned long ultimaLeitura = 0;
float stressScore = 0;

// ======== FUNÇÕES ========

void conectarWiFi() {
  Serial.print("Conectando ao WiFi...");
  WiFi.begin(WIFI_SSID, WIFI_PASS);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\n✅ Conectado ao WiFi virtual do Wokwi!");
  Serial.print("IP local: ");
  Serial.println(WiFi.localIP());
}

float lerNivelStress() {
  int valorADC = analogRead(PIN_POT);
  return (valorADC / 4095.0) * 100.0;
}

void enviarThingSpeak(float valor) {
  if (WiFi.status() != WL_CONNECTED) return;
  
  HTTPClient http;
  String url = String(THINGSPEAK_HOST) + "/update?api_key=" + THINGSPEAK_API_KEY + "&field1=" + String(valor, 1);
  
  http.begin(url);
  int codigo = http.GET();
  
  if (codigo > 0) {
    Serial.println("✅ Dados enviados ao ThingSpeak!");
  } else {
    Serial.println("⚠️  Erro ao enviar ao ThingSpeak");
  }
  http.end();
}

void enviarAzure(float valor) {
  if (WiFi.status() != WL_CONNECTED) return;

  HTTPClient http;
  String url = "http://" + String(AZURE_VM_IP) + ":" + String(AZURE_PORT) + "/";  // endpoint principal

  String json = "{\"stress_level\": " + String(valor, 1) + "}";

  http.begin(url);
  http.addHeader("Content-Type", "application/json");

  int codigo = http.POST(json);

  if (codigo > 0) {
    Serial.println("✅ Dados enviados à VM Azure!");
    Serial.print("Código HTTP: ");
    Serial.println(codigo);
  } else {
    Serial.println("⚠️  Falha ao enviar à VM Azure.");
  }
  http.end();
}

// ======== SETUP ========
void setup() {
  Serial.begin(115200);
  pinMode(PIN_LED, OUTPUT);
  digitalWrite(PIN_LED, LOW);
  conectarWiFi();
}

// ======== LOOP ========
void loop() {
  unsigned long agora = millis();

  // Leitura do sensor
  if (agora - ultimaLeitura >= SAMPLE_INTERVAL_MS) {
    ultimaLeitura = agora;
    stressScore = lerNivelStress();

    Serial.print("Nível de estresse: ");
    Serial.println(stressScore, 1);

    if (stressScore >= LIMIAR_STRESS) {
      digitalWrite(PIN_LED, HIGH);
      Serial.println("⚠️  Estresse elevado! Sugestão: fazer uma pausa.");
    } else {
      digitalWrite(PIN_LED, LOW);
    }
  }

  // Envio dos dados
  if (agora - ultimoEnvio >= ENVIO_INTERVALO_MS) {
    ultimoEnvio = agora;
    enviarThingSpeak(stressScore);
    enviarAzure(stressScore);
  }
}
