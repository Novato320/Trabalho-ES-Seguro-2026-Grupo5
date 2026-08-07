## Mitigações de segurança

### Controles mapeados às ameaças (STRIDE)

| ID  | Categoria STRIDE       | Ameaça identificada | Controle proposto |
|-----|------------------------|----------------------|--------------------|
| T01 | Spoofing               | Um atacante utiliza credenciais roubadas para acessar a conta de outro usuário | Autenticação multifator (MFA) para login e para operações sensíveis; bloqueio temporário da conta após múltiplas tentativas de login falhas; notificação ao usuário em caso de login em novo dispositivo |
| T02 | Tampering              | Um usuário altera indevidamente o valor, os itens ou o endereço de entrega de um pedido antes da confirmação | Validação de valores e itens no backend (nunca confiar apenas no que vem do cliente); recalcular o total do pedido no servidor; assinatura/hash do pedido no momento da confirmação para detectar alterações posteriores |
| T03 | Repudiation            | Um entregador ou cliente nega ter realizado (ou recebido) uma entrega, e o sistema não possui registros confiáveis | Confirmação de entrega por código gerado no app do cliente, foto do pedido entregue ou geolocalização do entregador no momento da confirmação; logs de auditoria imutáveis com timestamp para cada mudança de status do pedido |
| T04 | Information Disclosure | Um usuário acessa endereços, telefones ou histórico de pedidos de outro cliente sem autorização | Controle de autorização por objeto (verificar sempre se o recurso solicitado pertence ao usuário autenticado, e não apenas se ele está autenticado); uso de identificadores não sequenciais (UUID) para pedidos e usuários; criptografia de dados sensíveis em repouso e em trânsito (TLS) |
| T05 | Denial of Service      | Um atacante envia um grande volume de requisições durante o horário de pico, sobrecarregando o sistema | Rate limiting por usuário/IP na API; autoscaling da infraestrutura para absorver picos de demanda; uso de CDN e cache para reduzir carga direta no backend |
| T06 | Elevation of Privilege | Um estabelecimento ou entregador explora falha de autorização e acessa funções reservadas ao administrador | Controle de acesso baseado em papéis (RBAC), validado no backend a cada requisição; separação clara de rotas/endpoints administrativos, com verificação de permissão independente do frontend |

### Controles mapeados aos casos de abuso

| ID   | Caso de abuso | Controle proposto |
|------|----------------|--------------------|
| CA01 | Cancelamento ou alteração de pedido por meio de conta roubada | Exigir reautenticação (senha ou MFA) para operações sensíveis como cancelamento e alteração de endereço; enviar confirmação ao cliente (push/e-mail) antes de efetivar a mudança |
| CA02 | Entregador confirma entrega sem realizá-la | Exigir confirmação com código informado pelo cliente ou foto do pedido no local de entrega; liberar o pagamento ao entregador somente após a confirmação validada |
| CA03 | Consulta indevida de pedidos e dados de outros clientes | Validar no backend, em toda requisição, se o pedido/recurso solicitado pertence ao usuário autenticado; não expor identificadores previsíveis/sequenciais na API |
| CA04 | Indisponibilidade durante horário de pico | Implementar rate limiting e autoscaling; monitorar picos de tráfego com alertas automáticos; priorizar requisições críticas (checkout) em caso de sobrecarga |

### Considerações finais (mitigações)

Os controles propostos seguem o princípio de nunca confiar apenas em validações feitas no cliente (frontend), concentrando as verificações de integridade e autorização no backend. Priorizar a proteção de identidade (MFA), integridade dos pedidos (validação server-side) e autorização por objeto (evitar acesso a dados de terceiros) reduz o impacto das ameaças e casos de abuso com maior potencial de prejuízo financeiro e violação de privacidade, identificados na Etapa 1.