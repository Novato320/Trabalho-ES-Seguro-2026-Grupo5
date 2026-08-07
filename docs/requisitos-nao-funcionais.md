## Requisitos não-funcionais de segurança

Os requisitos abaixo formalizam, em regras objetivas e verificáveis, os controles de segurança propostos na análise de ameaças (STRIDE), nos casos de abuso e na matriz de risco.

### Autenticação e sessão

- RNF01 — O sistema deve exigir autenticação multifator (MFA) para login de clientes, estabelecimentos e entregadores.
- RNF02 — O sistema deve bloquear temporariamente a conta após 5 tentativas de login malsucedidas consecutivas.
- RNF03 — As senhas devem ter no mínimo 8 caracteres, combinando letras, números e símbolos.
- RNF04 — As sessões devem expirar automaticamente após 15 minutos de inatividade.
- RNF05 — O sistema deve notificar o usuário por e-mail ou push quando houver login em um novo dispositivo.

### Integridade dos dados

- RNF06 — O valor total, os itens e o endereço de entrega de um pedido devem ser validados e recalculados no backend antes da confirmação, independentemente do que for enviado pelo cliente.
- RNF07 — Toda alteração ou cancelamento de pedido após a confirmação deve exigir reautenticação do usuário.

### Confidencialidade

- RNF08 — Todos os dados sensíveis (pessoais e de pagamento) devem ser criptografados em trânsito (TLS 1.2 ou superior) e em repouso.
- RNF09 — O sistema deve validar, em toda requisição, se o recurso solicitado (pedido, endereço, dados de pagamento) pertence ao usuário autenticado.
- RNF10 — Identificadores de pedidos e usuários expostos pela API devem ser não sequenciais (ex.: UUID), evitando enumeração.

### Disponibilidade

- RNF11 — A API deve aplicar rate limiting por usuário e por IP, com limite configurável por endpoint.
- RNF12 — A infraestrutura deve suportar autoscaling para absorver picos de acesso acima de 3x a média diária.
- RNF13 — O sistema deve manter disponibilidade mínima de 99% durante os horários de pico (almoço e jantar).

### Autorização

- RNF14 — O controle de acesso deve seguir o modelo RBAC (controle baseado em papéis), validado no backend em toda requisição a rotas administrativas.
- RNF15 — Funções administrativas não devem ser acessíveis a contas de estabelecimento ou entregador, mesmo por manipulação direta de URLs ou parâmetros.

### Auditoria e rastreabilidade

- RNF16 — Toda mudança de status de um pedido (criado, confirmado, em entrega, entregue, cancelado) deve gerar um registro de auditoria com timestamp, imutável.
- RNF17 — A confirmação de entrega deve exigir código gerado pelo cliente, foto do pedido ou geolocalização do entregador no momento da confirmação.
- RNF18 — O pagamento ao entregador só deve ser liberado após validação da confirmação de entrega descrita em RNF17.