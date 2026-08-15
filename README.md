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

## 3. Visão geral do sistema

O sistema de delivery possui quatro perfis principais de usuário:

- **Cliente:** consulta estabelecimentos e produtos, realiza pedidos, efetua pagamentos e acompanha o andamento da entrega.
- **Estabelecimento:** gerencia produtos, preços, disponibilidade e pedidos recebidos.
- **Entregador:** visualiza entregas atribuídas e atualiza o status da entrega.
- **Administrador:** gerencia usuários, permissões, configurações e atividades administrativas do sistema.

Entre os principais ativos protegidos estão credenciais, dados pessoais,
endereços, informações de pagamento, pedidos, entregas e registros de
auditoria.

Os usuários interagem com o sistema por meio das interfaces da aplicação,
que se comunicam com a API/backend. O backend concentra as regras de negócio,
o controle de acesso e a comunicação com o banco de dados e serviços externos,
como pagamento e localização.

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

---

## 4. Etapa 1 — Modelagem de Ameaças e Casos de Abuso

### 4.1 [Modelagem de Ameaças com STRIDE](docs/modelagem-de-ameacas.md)

### 4.2 [Casos de abuso](docs/casos-de-abuso.md)

---

## 5. Etapa 2 — Análise, Priorização e Tratamento de Riscos

### 5.1 [Análise e priorização de riscos](docs/matriz-de-risco.md)

### 5.2 [Tratamento dos riscos com o NIST CSF](docs/mitigacoes.md)

### 5.3 [Resposta a incidentes](docs/resposta-a-incidentes.md)

---

## 6. Etapa 3 — Requisitos, Vulnerabilidades e Arquitetura Segura

### 6.1 [Requisitos de segurança e vulnerabilidades](docs/requisitos-e-vulnerabilidades.md)

### 6.2 [Arquitetura Segura e Decisões Arquiteturais](docs/arquitetura.md)

---

## 7. Etapa 4 — Código Seguro e Testes de Segurança

### 7.1 [Prática — Controle de autorização por recurso](pratica/pratica-autorizacao.md)

### 7.2 [Prática — Reautenticação para operações sensíveis](pratica/pratica-reautenticacao.md)

---

## 8. Etapa 5 — Verificação de Vulnerabilidades

### 8.1 [Análise da verificação com OWASP ZAP](docs/verificacao-de-vulnerabilidades.md)

### 8.2 [Evidências da execução](evidencias/etapa-5/)

---

## 9. Etapa 6 — Monitoramento e Detecção de Intrusões

### 9.1 [Roteiro de detecção de intrusões](roteiros/etapa-6-deteccao-de-intrusoes.md)

---

## 10. Etapa 7 — DevSecOps e Vídeo Final

### 10.1 [Pipeline DevSecOps e roteiro do vídeo](roteiros/etapa-7-devsecops-e-video-final.md)

### 10.2 Vídeo final

[▶️ Assistir à apresentação final no YouTube](https://youtu.be/UISsBoVC2ME)

---

## 11. Considerações finais

A análise mostrou que os principais riscos de segurança do sistema de delivery estão concentrados em três frentes: proteção de identidade e privilégios, proteção dos dados e integridade das operações, e disponibilidade do serviço.

Ao longo das etapas, as ameaças identificadas por meio do STRIDE foram
transformadas em riscos, requisitos de segurança e decisões arquiteturais.
Esses controles foram posteriormente relacionados a práticas de código seguro,
testes de segurança, verificação dinâmica, regras de detecção e ao pipeline
DevSecOps proposto.

O projeto demonstrou que a segurança deve acompanhar todo o ciclo de
desenvolvimento, desde a identificação inicial de ameaças até o monitoramento
e a resposta a incidentes após a implantação.
