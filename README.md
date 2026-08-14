# Sistema de Aplicativo de Delivery

## 1. Identificação do sistema

- **Nome do sistema:** Sistema de Aplicativo de Delivery
- **Integrantes:** Guilherme Jaques, Gabriel Ortiz e Gabriel Dornelles
- **Repositório:** https://github.com/Novato320/Trabalho-ES-Seguro-2026-Grupo5
- **Justificativa:** O sistema foi escolhido por possuir diferentes perfis de usuários, armazenar informações pessoais, endereços e dados de pedidos, além de executar operações críticas, como realização de compras, processamento de pagamentos, acompanhamento de entregas e cancelamento de pedidos.

> **Observação:** Este é um modelo didático inspirado em um aplicativo de delivery, não representando a implementação de nenhuma plataforma comercial existente.

## 2. Descrição do sistema

O Sistema de Aplicativo de Delivery permite que clientes consultem estabelecimentos parceiros, visualizem cardápios, realizem pedidos, efetuem pagamentos e acompanhem o status da entrega em tempo real. Estabelecimentos podem gerenciar seus produtos, preços, pedidos e disponibilidade, enquanto entregadores visualizam as entregas atribuídas e atualizam o andamento das entregas. O sistema armazena dados pessoais dos usuários, endereços, informações de pagamento, histórico de pedidos, registros das entregas e logs das operações realizadas.

---

## 3. Usuários, ativos e pontos de interação
### 3.1 Usuários e perfis de acesso

- **Cliente:** consulta estabelecimentos e produtos, realiza pedidos, efetua pagamentos e acompanha o andamento da entrega.
- **Estabelecimento:** gerencia produtos, preços, disponibilidade e pedidos recebidos.
- **Entregador:** visualiza entregas atribuídas e atualiza o status da entrega.
- **Administrador:** gerencia usuários, permissões, configurações e atividades administrativas do sistema.

### 3.2 Ativos importantes

Os principais ativos que precisam ser protegidos são:

- credenciais de acesso dos usuários;
- dados pessoais, como nome, telefone e e-mail;
- endereços e informações de localização;
- histórico e informações dos pedidos;
- dados relacionados aos pagamentos;
- informações dos estabelecimentos e entregadores;
- avaliações realizadas pelos usuários;
- registros e logs das operações;
- banco de dados do sistema.

O acesso, alteração, divulgação ou indisponibilidade indevida desses ativos pode causar prejuízos financeiros, exposição de informações pessoais, fraudes e perda de confiança no sistema.

### 3.3 Pontos de interação

Os principais pontos de interação do sistema são:

- aplicativo ou interface utilizada pelo cliente;
- interface utilizada pelos estabelecimentos;
- interface utilizada pelos entregadores;
- API/backend do sistema;
- banco de dados;
- serviço externo de pagamento;
- serviços de localização e mapas.

## 4. Visão geral do sistema

### Fluxo funcional simplificado

```text
Cliente ──────────────┐
                      │
Estabelecimento ──────┼──→ API / Backend ───→ Banco de Dados
                      │          │
Entregador ───────────┘          ├──→ Serviço de Pagamento
                                 │
                                 └──→ Serviço de Mapas / Localização

```                       
Este fluxo apresenta apenas a comunicação geral entre os principais
componentes. A arquitetura com os controles de segurança definidos ao longo
do projeto está detalhada na [Etapa 3 — Arquitetura Segura](docs/arquitetura.md).

## 5. Etapa 1 — Modelagem de Ameaças e Casos de Abuso

### 5.1 [Modelagem de Ameaças com STRIDE](docs/modelagem-de-ameacas.md)

### 5.2 [Casos de abuso](docs/casos-de-abuso.md)

---

## 6. Etapa 2 - Análise, Priorização e Tratamento de Riscos

### 6.1 [Análise e priorização de riscos](docs/matriz-de-risco.md)

### 6.2 [Tratamento dos riscos com o NIST CSF](docs/mitigacoes.md)

### 6.3 [Resposta a incidentes](docs/resposta-a-incidentes.md)

---

## 7. Etapa 3 — Requisitos, Vulnerabilidades e Arquitetura Segura

### 7.1 [Requisitos de segurança e vulnerabilidades](docs/requisitos-e-vulnerabilidades.md)

### 7.2 [Arquitetura Segura e Decisões Arquiteturais](docs/arquitetura.md)

---

## 8. Etapa 4 — Código Seguro e Testes de Segurança

### 8.1 [Prática — Controle de autorização por recurso](pratica/pratica-autorizacao.md)

### 8.2 [Prática — Reautenticação para operações sensíveis](pratica/pratica-reautenticacao.md)
---

## 9. Etapa 5 — Verificação de Vulnerabilidades

### 9.1 [Análise da verificação com OWASP ZAP](docs/verificacao-de-vulnerabilidades.md)

### 9.2 [Evidências da execução](evidencias/etapa-5/)

---

## 10. Etapa 6 — Monitoramento e Detecção de Intrusões

### 10.1 [Roteiro de detecção de intrusões](roteiros/etapa-6-deteccao-de-intrusoes.md)

---

## 11. Etapa 7 — DevSecOps e Vídeo Final

### 11.1 [Pipeline DevSecOps e roteiro do vídeo](roteiros/etapa-7-devsecops-e-video-final.md)

### 11.2 Vídeo final

[INSERIR LINK OU LOCAL DO VÍDEO]

---

## 12. Considerações finais

A análise mostrou que os principais riscos de segurança do sistema de delivery estão concentrados em três frentes: proteção de identidade e privilégios, proteção dos dados e integridade das operações, e disponibilidade do serviço.

Ao longo das etapas, as ameaças identificadas por meio do STRIDE foram
transformadas em riscos, requisitos de segurança e decisões arquiteturais.
Esses controles foram posteriormente relacionados a práticas de código seguro,
testes de segurança, verificação dinâmica, regras de detecção e ao pipeline
DevSecOps proposto.

O projeto demonstrou que a segurança deve acompanhar todo o ciclo de
desenvolvimento, desde a identificação inicial de ameaças até o monitoramento
e a resposta a incidentes após a implantação.
