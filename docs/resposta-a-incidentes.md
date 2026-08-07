## Política de resposta a incidentes

Esta política define os passos a serem seguidos caso uma das ameaças identificadas na Etapa 1 se concretize, com o objetivo de conter o incidente, minimizar o impacto e restaurar a operação normal do sistema.

### Etapas gerais de resposta

1. **Identificação** — o incidente é detectado por monitoramento automático (ex.: pico anormal de requisições, múltiplas tentativas de login falhas, alteração de dados fora do padrão) ou reportado por um usuário (cliente, estabelecimento ou entregador).
2. **Contenção** — ação imediata para limitar o dano, como bloquear a conta afetada, revogar sessões ativas ou pausar temporariamente uma funcionalidade específica.
3. **Erradicação** — remoção da causa raiz do incidente (ex.: revogar credenciais comprometidas, corrigir falha de autorização explorada).
4. **Recuperação** — restauração do serviço afetado, validação de que o problema foi resolvido antes de liberar o acesso normalmente.
5. **Comunicação** — notificação aos usuários afetados e, se necessário, registro formal do incidente para fins de auditoria e conformidade.
6. **Análise pós-incidente** — revisão do ocorrido para identificar melhorias nos controles existentes e evitar recorrência.

### Ações específicas por tipo de incidente

| Incidente | Ação imediata (contenção) | Responsável | Ação de recuperação |
|-----------|---------------------------|--------------|-----------------------|
| Conta comprometida (T01/CA01) | Bloquear a conta e revogar sessões ativas; exigir redefinição de senha e MFA | Equipe de segurança/suporte | Restaurar acesso após verificação de identidade do usuário legítimo |
| Pedido alterado indevidamente (T02) | Reverter o pedido para o último estado íntegro registrado no log de auditoria | Equipe de backend | Notificar cliente e estabelecimento sobre a correção |
| Confirmação de entrega fraudulenta (T03/CA02) | Suspender temporariamente o repasse do pagamento ao entregador envolvido | Equipe de operações | Investigar com base em geolocalização/foto; liberar ou estornar o pagamento conforme apuração |
| Vazamento/acesso indevido a dados (T04/CA03) | Revogar tokens de acesso; corrigir a falha de autorização explorada | Equipe de segurança | Notificar os usuários cujos dados foram expostos, conforme exigido pela LGPD |
| Indisponibilidade do sistema (T05/CA04) | Acionar autoscaling; ativar rate limiting mais restritivo temporariamente | Equipe de infraestrutura | Monitorar estabilização e comunicar usuários sobre a normalização do serviço |
| Acesso indevido a funções administrativas (T06) | Revogar as permissões da conta envolvida; bloquear o acesso à rota explorada | Equipe de segurança | Corrigir a falha de RBAC e revisar permissões de todas as contas do mesmo tipo |

### Registro e conformidade

Todo incidente de segurança deve ser documentado, contendo: data e hora, tipo de incidente, usuários/dados afetados, ações tomadas e tempo de resolução. Incidentes que envolvam exposição de dados pessoais devem seguir os prazos e obrigações de notificação previstos na LGPD.