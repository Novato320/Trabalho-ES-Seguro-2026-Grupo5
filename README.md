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

## 4. Visão geral da arquitetura ou fluxo

O sistema é composto por diferentes tipos de usuários que acessam a aplicação por meio de suas respectivas interfaces. Essas interfaces se comunicam com uma API/backend responsável por processar as requisições, aplicar as regras de negócio e controlar o acesso às funcionalidades.

O backend acessa o banco de dados para armazenar e consultar informações como usuários, estabelecimentos, produtos, pedidos, entregas, pagamentos e registros das operações.

Além disso, o sistema pode se comunicar com serviços externos, como uma plataforma de pagamento e serviços de mapas e localização.

### Fluxo simplificado

```text
Cliente ──────────────┐
                      │
Estabelecimento ──────┼──→ API / Backend ───→ Banco de Dados
                      │          │
Entregador ───────────┘          ├──→ Serviço de Pagamento
                                 │
                                 └──→ Serviço de Mapas / Localização

```                       

## 5. [Modelagem de Ameaças com STRIDE](docs/modelagem-de-ameacas.md)

## 6. [Casos de abuso](docs/casos-de-abuso.md)

## 7. Considerações finais

As ameaças consideradas mais preocupantes são o acesso indevido a contas de clientes e entregadores, a alteração de pedidos e pagamentos, e a indisponibilidade do sistema em horários de pico.

Os ativos mais importantes são as credenciais de acesso, os dados pessoais e de pagamento, os endereços de entrega, o histórico de pedidos e os registros (logs) das operações.

Os casos de abuso com maior impacto são a confirmação falsa de entrega e o acesso a pedidos/dados de terceiros por meio de conta roubada, pois envolvem diretamente prejuízo financeiro e violação de privacidade dos clientes.

A principal dificuldade da análise foi diferenciar uma ameaça genérica de uma situação concreta relacionada ao sistema de delivery. A utilização do STRIDE ajudou a examinar o software sob diferentes perspectivas e a identificar ameaças que poderiam não ser percebidas em uma análise apenas funcional.

---

## 8. Análise e priorização de riscos

## 9. Tratamento dos riscos com o NIST CSF

---
