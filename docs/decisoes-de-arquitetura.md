# Decisões de arquitetura segura

As decisões apresentadas a seguir foram definidas a partir dos riscos prioritários e dos requisitos de segurança selecionados na Etapa 3.

## DA01 — Limitação de requisições na API

**Risco tratado:** R05 — Indisponibilidade do sistema

**Decisão tomada:** Aplicar rate limiting na entrada da API para operações críticas.

**Motivo:** Limitar a quantidade de requisições por usuário ou endereço IP reduz a possibilidade de esgotamento dos recursos do backend durante picos de acesso ou tráfego malicioso.

**Componente afetado:** API / Backend.

**Resultado esperado:** Manter o sistema disponível para usuários legítimos mesmo diante de um volume elevado de requisições.

## DA02 — Autorização por recurso no backend

**Risco tratado:** R04 — Acesso indevido a dados de outros usuários

**Decisão tomada:** Validar no backend se o usuário autenticado possui autorização sobre o recurso solicitado antes de permitir sua consulta ou alteração.

**Motivo:** Restrições existentes apenas no frontend podem ser contornadas por meio da manipulação direta das requisições e dos identificadores dos recursos.

**Componente afetado:** API / Backend, Serviço de Pedidos e mecanismo de autorização.

**Resultado esperado:** Impedir que usuários consultem ou alterem pedidos, endereços, dados pessoais ou informações de pagamento pertencentes a terceiros.

## DA03 — Verificação adicional para operações sensíveis

**Risco tratado:** R01 — Uso indevido de conta

**Decisão tomada:** Exigir nova autenticação ou segundo fator antes da execução de operações sensíveis.

**Motivo:** Credenciais comprometidas não devem ser suficientes para permitir operações de maior impacto na conta da vítima.

**Componente afetado:** Serviço de autenticação e API / Backend.

**Resultado esperado:** Reduzir a possibilidade de pagamentos, cancelamentos ou alterações de dados serem realizados por um atacante utilizando credenciais comprometidas.

## Rastreabilidade da Etapa 3

| Risco | Requisito | Vulnerabilidade relacionada | Decisão de arquitetura | Controle no diagrama |
|---|---|---|---|---|
| R05 | RS01 | OWASP API4:2023 / CWE-770 | DA01 | Rate limiting por IP/usuário na camada de borda/API |
| R04 | RS02 | OWASP A01:2021 / CWE-639 | DA02 | Autorização por recurso no backend/Serviço de Pedidos |
| R01 | RS03 | OWASP A07:2021 / CWE-287 | DA03 | MFA e reautenticação no Serviço de Autenticação |
