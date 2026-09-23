
# ✈️ Automação Inteligente de Documentos de Viagem: Pipeline com Google Gemini

![Google Apps Script](https://img.shields.io/badge/Google%20Apps%20Script-4285F4?style=for-the-badge&logo=google&logoColor=white)
![Google Gemini](https://img.shields.io/badge/Google%20Gemini-8E75B2?style=for-the-badge&logo=googlebard&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)
![API Integration](https://img.shields.io/badge/REST_API-Integration-success?style=for-the-badge)

## 📌 Visão Geral do Projeto
Este projeto é uma solução serverless construída e 100% integrada ao ecossistema Google Workspace para extração inteligente, estruturação contábil e processamento automatizado de bilhetes aéreos e reservas de hotéis (PDF) para o Google Sheets.

O objetivo do painel/pipeline é eliminar processos manuais de digitação, que costumam ser lentos e suscetíveis a erros humanos devido à variedade de layouts (Companhias aéreas, GDSs e consolidadoras), otimizando a produtividade do back-office da agência.

---

## 📊 Impacto e Resultados Alcançados

A implementação deste *pipeline* gerou um impacto direto na operação e produtividade da equipa de *back-office*:

* **Redução Drástica de Tempo (SLA):** O processamento de um bilhete, que demorava em média **5 a 7 minutos** manualmente, passou a ser extraído e contabilizado em **menos de 30 segundos** (uma redução de tempo superior a 90%).
* **Mitigação de Riscos e Erros Humanos:** Redução drástica nas falhas operacionais, como erros de digitação em valores de comissão (RAV/DU) ou troca de datas, que antes geravam prejuízos financeiros para a agência.
* **Ganho de Capacidade Produtiva:** A equipe poupou dezenas de horas mensais em trabalho repetitivo, redirecionando o foco para o atendimento consultivo e vendas de alto valor acrescentado.


## 👨‍💻 Escopo de Atuação (End-to-End)
Neste projeto, fui o responsável integral pelo ciclo de vida da automação, atuando desde o desenho da arquitetura até o deploy do script. Minhas responsabilidades incluíram:

* **Arquitetura Serverless:** Estruturação e orquestração do fluxo automatizado diretamente no Google Workspace (Drive -> Apps Script -> Sheets), dispensando servidores externos.
* **Integração de IA e Prompt Engineering:** Elaboração de payloads HTTP, conversão de arquivos para Base64 e construção de prompts estruturados para a API do Google Gemini garantir respostas em JSON consistentes.
* **Desenvolvimento e Regras de Negócio:** Codificação completa em JavaScript (ES6), implementando lógicas contábeis avançadas, tratamento de exceções de API e injeção de fórmulas dinâmicas na folha de cálculo.

## 🎯 Desafios de Negócio Resolvidos

O projeto foi estruturado integrando engenharia de software e regras de negócio para responder a 4 desafios principais:

### 1. Extração de Dados em Layouts Despadronizados
* **O Desafio:** Consolidar dados precisos a partir de uma variedade enorme de layouts de PDF (LATAM, Azul, TAP, Air France, Trend/Bee), impossibilitando o uso de ferramentas tradicionais de OCR baseadas em coordenadas fixas.
* **A Solução:** Utilizando a IA Multimodal (Google Gemini API), orquestrei um fluxo de extração semântica. O PDF é convertido em Base64 e processado pelo modelo, que entende o contexto do documento e retorna um objeto JSON perfeitamente estruturado, independentemente da formatação original do arquivo.

  <img width="1597" height="727" alt="image" src="https://github.com/user-attachments/assets/ff9a9bed-2173-462f-a2c7-24a4ed6bcd03" />

### 2. Regras de Negócio e Contabilidade Automatizada
* **O Desafio:** Garantir que reservas complexas (múltiplos passageiros ou consolidação de hotéis) fossem desmembradas corretamente e que comissões fossem calculadas sem margem de erro.
* **A Solução:** Desenvolvimento de scripts com lógicas condicionais rigorosas:
  * **Passagens Aéreas:** Divisão automática de reservas com múltiplos passageiros em linhas sequenciais individuais.
  * **Normalização de Nomes:** Conversão do padrão IATA (`SOBRENOME/NOME`) para ordem direta natural (`NOME SOBRENOME`) em maiúsculas.
  * **Hotéis e RAV:** Consolidação de vouchers em linha única no nome do titular, com cálculo automático e isolamento de comissões (ex.: deduzindo a tarifa líquida e aplicando 12% de RAV sobre pacotes de hotelaria).

<img width="1595" height="179" alt="image" src="https://github.com/user-attachments/assets/6f0bcdb8-ee0b-495c-9f41-b02668144c98" />
<img width="808" height="113" alt="image" src="https://github.com/user-attachments/assets/5ec3ec23-5f1d-498b-ac77-8405d466a404" />


### 3. Robustez contra Limites de Taxa da API (HTTP 429 / 503)
* **O Desafio:** Prevenir que picos de tráfego (muitos PDFs processados de uma vez) esgotassem o limite do plano gratuito do Gemini (Tokens Per Minute), gerando falhas na operação.
* **A Solução:** Implementação de uma arquitetura resiliente com *Fallback Cascata de Modelos* (alternância automática entre `gemini-flash-lite-latest`, `gemini-flash-latest` e `gemini-pro-latest`) e estratégias de *Backoff* e *Throttle* (pausas com `Utilities.sleep()`) para respeitar os limites de requisição.

  <img width="1083" height="320" alt="image" src="https://github.com/user-attachments/assets/e790a9d7-9667-4f6b-a7d2-2a3d4a510f47" />


### 4. Injeção Dinâmica e Saneamento da Planilha
* **O Desafio:** Inserir os dados na planilha de forma que permitissem edições humanas posteriores sem quebrar cálculos, além de contornar bugs de formatação do próprio Sheets que empurram os dados para milhares de linhas abaixo.
* **A Solução:** 
  * Criação de uma rotina de varredura que identifica a "primeira linha vazia real" com base na coluna de passageiros.
  * Injeção de fórmulas ativas nativas em inglês (`=SUM(...)`) amarradas diretamente na linha processada, garantindo recálculo em tempo real se a agência alterar alguma tarifa manualmente (com conversão nativa automática para `=SOMA()` no Brasil).

  <img width="675" height="81" alt="image" src="https://github.com/user-attachments/assets/5abdd5e4-81f3-4d28-8e5b-bdc8536dd4f4" />



---

## 🛠️ Tecnologias e Habilidades Aplicadas

* **Linguagens e Ambientes:** JavaScript (ES6) rodando no ecossistema serverless do Google Apps Script.
* **Inteligência Artificial:** Google AI Studio, Gemini API (`gemini-1.5-flash`), Prompt Engineering Multimodal (texto + imagem/pdf).
* **Integrações (APIs REST):** Comunicação HTTP(S), manipulação de Headers, Autenticação Baseada em Chave (`X-goog-api-key`), parsing de JSON e conversão de binários (Base64).
* **Automação Workspace:** Manipulação avançada do Google Drive API (vigilância de pastas via Triggers temporizados) e Google Sheets API.

---

## 🎓 Formações e Certificações

* **Certificado Profissional Google Data Analytics** – Coursera / Google (2026)
* **Google Prompting Essentials (Inteligência Artificial)** – Coursera / Google (2026)
* **Business Intelligence com Power BI** – Witseed (2025)
* **Fundamentos de Python (Pandas, NumPy, EDA e ETL)** – Witseed (2025)
* **Liderança, Planejamento e Gestão de Pessoas (90h)** – Instituto Educacional Aprender (2026)
* **Excel Avançado** – Centro Educacional Brastemp (2024)
* **Licenciatura em Música e Bacharelado em Administração (Cursando)**

---
---

*Gostou do projeto ou tem alguma dúvida sobre o código e as integrações via API? Sinta-se à vontade para conectar-se comigo!*

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/lucas-santana-98251325a)
[![E-mail](https://img.shields.io/badge/Email-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:lucas_santana321@outlook.com)
