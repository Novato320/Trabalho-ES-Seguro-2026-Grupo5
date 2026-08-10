# Requisitos de Segurança e Vulnerabilidades Catalogadas

Este documento apresenta os requisitos de segurança derivados dos riscos críticos identificados na análise de riscos, bem como o mapeamento de vulnerabilidades catalogadas associadas a cada risco.

## Requisitos de segurança

Foram selecionados os três riscos de nível crítico com maior prioridade na análise de riscos (R05, R04 e R01), e derivado um requisito de segurança específico e verificável para cada um.

| ID | Risco | Requisito de segurança | Critério de verificação |
|---|---|---|---|
| RS01 | R05 | O sistema deverá limitar a taxa de requisições por usuário/IP nas operações críticas da API (ex: criação de pedidos, login), rejeitando ou colocando em fila requisições que excedam o limite definido | Requisições acima do limite configurado deverão ser recusadas (ex: resposta HTTP 429), e o sistema deverá permanecer disponível para os demais usuários durante um teste de carga simulando pico de acesso |
| RS02 | R04 | O sistema deverá validar no backend, em toda requisição, se o usuário autenticado é o proprietário do recurso solicitado (dados pessoais, endereços, pedidos e pagamentos) antes de retornar ou alterar qualquer informação | A tentativa de acesso a um recurso pertencente a outro usuário deverá ser recusada (ex: resposta 403/404), mesmo que o identificador do recurso seja manipulado diretamente na requisição |
| RS03 | R01 | O sistema deverá exigir uma verificação adicional (nova autenticação ou segundo fator) antes de confirmar operações sensíveis, como pagamento, alteração de dados cadastrais ou cancelamento de pedido | A operação sensível deverá ser recusada sempre que a verificação adicional não for concluída com sucesso |

## Vulnerabilidades catalogadas

Para cada requisito, foi pesquisada uma vulnerabilidade relacionada em catálogos reconhecidos (CWE e OWASP).

| Risco | Vulnerabilidade ou categoria | Referência utilizada | Relação com o sistema |
|---|---|---|---|
| R05 | Consumo irrestrito de recursos / falta de limitação de requisições | OWASP API4:2023 – Unrestricted Resource Consumption (CWE-770: Allocation of Resources Without Limits or Throttling) | A ausência de limitação de requisições permite que um grande volume de acessos (legítimo em horário de pico ou malicioso) esgote os recursos do backend, derrubando o serviço para clientes, estabelecimentos e entregadores |
| R04 | Quebra de controle de acesso (IDOR – Insecure Direct Object Reference) | OWASP A01:2021 – Broken Access Control (CWE-639: Authorization Bypass Through User-Controlled Key) | A falha em validar no servidor se o usuário é dono do recurso solicitado permite que, ao manipular um identificador (ID de pedido, endereço ou pagamento), um usuário acesse dados de outra pessoa |
| R01 | Falha de identificação e autenticação | OWASP A07:2021 – Identification and Authentication Failures (CWE-287: Improper Authentication) | A falta de uma etapa extra de verificação para operações sensíveis faz com que credenciais comprometidas (roubadas ou reutilizadas) sejam suficientes para um atacante realizar pedidos e operações em nome da vítima |