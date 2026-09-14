# PARECER TÉCNICO DE ARQUITETURA ENTERPRISE
**Estratégia de Expansão de Portfólio, Engajamento e Modernização do Core Bancário**

* **Preparado por:** Time de Enterprise Architecture (EA)
* **Destinatários:** C-Level, CTO e CIO
* **Data:** Setembro de 2026
* **Status:** Recomendação Oficial (Direcionamento Estratégico)
* **Escopo:** Avaliação de Conta de Pagamentos vs. Cashback, Plataforma de Core Bancário e Quebra de Silos Legados.

---

## 1. Sumário Executivo & Decisão Recomendada
Este parecer técnico apresenta o direcionamento estratégico do time de Enterprise Architecture para a tomada de decisão do C-Level referente ao aumento de portfólio, engajamento e evolução da arquitetura corporativa. Após análise holística das capacidades e restrições do banco, apresentamos as seguintes decisões macro estruturadas:

* **Produto Recomendado: Conta de Pagamentos.** A EA recomenda a priorização da Conta de Pagamentos sobre o Cashback. A conta atua como um hub gravitacional de engajamento diário e é a base mandatória para a cross-sell efetiva dos produtos de crédito existentes.
* **Abordagem do Core Bancário: Modular de Nova Geração (Next-Gen Core) focado em Ledger.** A EA desaconselha a aquisição de um Core Bancário tradicional monolítico que substitua todo o legado de uma vez. Em vez disso, recomenda-se a aquisição de uma plataforma moderna de Core Ledger baseada em microsserviços via APIs para suportar unicamente a Conta de Pagamentos e, progressivamente, expor os produtos de empréstimo através de uma camada de capacidades unificadas.
* **Estilo Arquitetural Preservado: Microsserviços.** O novo ecossistema respeitará estritamente a arquitetura descentralizada existente, quebrando os silos operacionais através de um modelo de Bounded Contexts e barramento de eventos (Event-Driven Architecture).

---

## 2. Mapa de Problemas de Negócios (Subdomínios DDD)
Para organizar a complexidade atual e o novo cenário, dividimos o banco sob a ótica de Domain-Driven Design (DDD), permitindo identificar onde reside o valor diferencial e as ineficiências operacionais:

| Subdomínio | Classificação | Situação Atual & Impacto na Cadeia |
| :--- | :--- | :--- |
| **Crédito e Empréstimos** *(CDC, Pessoal, Consignado, Garantias)* | CORE DOMAIN (Diferencial) | Construído em silos verticais isolados. Alto custo de manutenção, sem reaproveitamento de componentes de cálculo ou motores de crédito, impactando severamente o Time-to-Market de novos produtos. |
| **Conta de Pagamentos** *(Novo Produto solicitado)* | CORE DOMAIN (Alvo) | Inexistente. Irá atuar como principal ponto de contato e atração de depósitos de baixo custo (funding) para alavancar a operação de crédito. |
| **Cashback e Parcerias** *(Produto Alternativo)* | SUPPORTING (Suporte) | Gera engajamento, mas depende de transacionalidade prévia. Isolado, não resolve o problema estrutural de retenção ou funding do banco. |
| **Core Transacional / Ledger** *(Saldos e Lançamentos)* | GENERIC (Genérico) | Inexistente para contas. A provocação do CTO faz sentido ao buscar uma plataforma de mercado (SaaS/Cloud-Native) para este fim genérico, liberando a engenharia interna para focar no Core Domain. |

---

## 3. Justificativa e Lastreio da Tomada de Decisão
A escolha da Conta de Pagamentos e a estratégia para o Core Bancário baseiam-se em robustos pilares de arquitetura e viabilidade de negócio:

### Por que Conta de Pagamentos e não Cashback?
1. **Geração de Funding:** A conta de pagamentos permite a captação de depósitos à vista. Esse capital reduz drasticamente o custo de captação (funding) do banco, aumentando a margem líquida (spread) das operações de empréstimos, que são o atual motor financeiro da empresa.
2. **Frequência de Uso (Engajamento Real):** Clientes usam cashback pontualmente. A conta corrente/pagamento é utilizada diariamente (Pix, pagamento de boletos, transferências), criando uma volumetria de dados de comportamento essencial para refinar os modelos de score de crédito do banco.
3. **Prontidão para Cross-Sell:** É infinitamente mais natural ofertar um Crédito Consignado ou Pessoal para quem já possui movimentação e saldo no banco do que para um usuário que apenas consome pontos de cashback externos.

### Lastreio sobre a plataforma de Core Bancário (Visão CTO):
A provocação do CTO é válida, mas requer um ajuste de escopo arquitetural. Substituir o legado inteiro por um Core tradicional geraria um projeto de alto risco ('Big Bang') de 3 a 5 anos. A recomendação de EA é a aquisição de um Core Ledger Cloud-Native / API-First unicamente para gerenciar o saldo e os lançamentos da nova Conta de Pagamentos. Os produtos de empréstimos legados continuarão rodando temporariamente nos seus motores atuais, mas passarão a interagir com o ecossistema através de uma camada unificada de APIs de Integração.

---

## 4. Mapa de Capacidades de Negócios e Classificações
Abaixo estruturamos o Mapa de Capacidades corporativo sob o modelo de arquitetura TO-BE, demonstrando a classificação de criticidade e agrupamento funcional de cada uma para eliminar os silos atuais:

| Grupo Funcional | Capacidade de Negócio (Business Capability) | Classificação | Estratégia de Integração / Evolução |
| :--- | :--- | :--- | :--- |
| **Customer Facing & Channels** | Gestão de Identidade e Onboarding <br> Movimentação Digital (Pix/TED) | Diferencial / Crítica | Unificada via Microsserviço de Canais. O onboarding da conta serve para reaproveitar no crédito. |
| **Product Management** | Ciclo de Vida de Empréstimos (Legado) | Core Operacional | Encapsular o legado em APIs estruturadas. |
| **Product Management** | Ciclo de Vida de Depósitos/Pagamentos | Novo Core Ledger | Centralizar depósitos na nova plataforma de mercado comprada. |
| **Risk & Ledger (Back-Office)** | Motor de Análise de Risco de Crédito | Estratégica | Unificar os motores de análise em um microsserviço compartilhado. |
| **Risk & Ledger (Back-Office)** | Contabilidade Transacional (Ledger) | Commodity Altamente Crítica | Usar o novo Core para o Ledger genérico. |

---

## 5. Cadeia de Valor Corporativa & Interoperabilidade
Para eliminar o impacto negativo dos silos na cadeia de valor, a arquitetura TO-BE redesenha o fluxo operacional agrupando capacidades transversais de forma que os produtos de empréstimo existentes orbitem ao redor da nova Conta de Pagamentos.

### Agrupamentos Funcionais Transversais Propostos:
1. **Camada de Experiência (Experiencia Unificada):** Apps e Internet Banking conectados a um API Gateway centralizado, criando a eliminação dos silos de canais dos empréstimos.
2. **Camada de Integração e Orquestração (Microsserviços de Negócio):** Componentes reutilizáveis como 'Motor de Crédito Unificado', 'Originação de Propostas' e 'Gestão de Limites'. Qualquer novo produto consome os mesmos blocos de arquitetura.
3. **Camada Core e Registro:** Divisão clara entre o legado de crédito encapsulado por APIs e o novo Core Ledger comprado focado exclusivamente em depósitos e transações de pagamento.

---

## 6. Plano de Migração de Arquitetura (Roadmap Estratégico)
A transição das capacidades em silos atuais para a arquitetura unificada baseada em microsserviços e próxima geração de core será executada em três horizontes lógicos para evitar disrupções nas operações correntes:

| Fase / Horizonte | Foco de Entrega (Escopo) | Resultados de Arquitetura e Negócio |
| :--- | :--- | :--- |
| **Fase 1: Fundação & Novo Produto** <br> *(Meses 1 a 6)* | • Aquisição e deploy do Core Ledger via SaaS/Cloud. <br> • Criação do Microsserviço de Conta de Pagamentos. <br> • Implementação do API Gateway corporativo. | Lançamento da Conta de Pagamentos. <br> Criação do canal unificado de entrada. <br> Início da captura de engajamento diário. |
| **Fase 2: Desacoplamento & Abstração** <br> *(Meses 6 a 12)* | • Construção da camada de abstração (APIs) sobre os silos de empréstimo (CDC, Cartões, Pessoal). <br> • Unificação do Motor de Risco de Crédito em microsserviço único. | Eliminação parcial dos silos. Os produtos legados passam a expor suas capacidades de forma padronizada para reuso imediato. |
| **Fase 3: Ecossistema Unificado** <br> *(Meses 12 a 18)* | • Orquestração de fluxos transversais (Ex: usar saldo da conta para quitar parcelas de empréstimos automaticamente). <br> • Migração opcional de sistemas legados obsoletos para o novo Core. | Cadeia de valor otimizada. Redução do Time-to-Market para novos produtos em até 70%. Interoperabilidade completa. |

---

## 7. Próximos Passos recomendados ao Comitê
1. Aprovação formal do comitê executivo para a priorização do produto de Conta de Pagamentos.
2. Início do processo de RFI/RFP para seleção da plataforma de mercado de Core Ledger Cloud-Native/API-First.
3. Alocação do time de arquitetura de solução para detalhamento do desenho técnico das APIs de abstração do legado de crédito.

---
*CONFIDENCIAL - Uso Interno | Banco Paulista de Expansão | Página 5 de 5*
