## 6. Casos de abuso

### CA01 — Cancelamento ou alteração de pedido por meio de conta roubada

**Ator:** atacante externo.

**Objetivo:** causar prejuízo a um cliente ou estabelecimento cancelando ou alterando um pedido já pago.

**Condições necessárias:**
- o atacante obtém as credenciais da vítima;
- o sistema não exige verificação adicional para operações sensíveis (cancelamento, alteração de endereço);
- a conta pode ser acessada apenas com usuário e senha.

**Fluxo de abuso:**
1. O atacante obtém o usuário e a senha do cliente.
2. O atacante acessa o sistema utilizando a identidade da vítima.
3. O atacante consulta os pedidos em andamento.
4. O atacante cancela o pedido ou altera o endereço de entrega.
5. O cliente percebe o problema apenas após o ocorrido.

**Impacto esperado:** prejuízo financeiro, perda do pedido/pagamento, necessidade de contestação e reembolso.

**Categorias STRIDE relacionadas:** Spoofing, Tampering e Repudiation.

---

### CA02 — Entregador confirma entrega sem realizá-la

**Ator:** entregador mal-intencionado.

**Objetivo:** receber o pagamento pela entrega sem efetivamente entregar o pedido ao cliente.

**Condições necessárias:**
- o sistema confia apenas na confirmação feita pelo próprio entregador;
- não há verificação adicional (foto, código de confirmação, geolocalização) no momento da entrega;
- o pagamento ao entregador é liberado com base apenas nesse status.

**Fluxo de abuso:**
1. O entregador retira o pedido no estabelecimento.
2. O entregador marca o pedido como "entregue" no aplicativo sem entregá-lo.
3. O sistema libera o pagamento ao entregador.
4. O cliente não recebe o pedido e precisa abrir uma contestação.

**Impacto esperado:** prejuízo ao cliente, custo de reembolso ao estabelecimento/plataforma, perda de confiança no serviço de entrega.

**Categorias STRIDE relacionadas:** Repudiation e Tampering.

---

### CA03 — Consulta indevida de pedidos e dados de outros clientes

**Ator:** usuário autenticado sem autorização para consultar dados de terceiros.

**Objetivo:** obter dados pessoais, endereços ou histórico de pedidos de outros clientes.

**Condições necessárias:**
- o sistema não verifica corretamente a quem pertence o pedido solicitado;
- identificadores de pedidos ou usuários podem ser modificados diretamente na requisição (ex.: trocar o ID na URL);
- a API retorna os dados sem validar a autorização.

**Fluxo de abuso:**
1. O usuário acessa seus próprios pedidos normalmente.
2. O usuário modifica o identificador do pedido presente na requisição.
3. O sistema retorna dados de um pedido pertencente a outro cliente.
4. O usuário repete a operação para consultar diversos registros.
5. As informações coletadas são armazenadas ou usadas indevidamente.

**Impacto esperado:** violação de privacidade, exposição de endereços e dados pessoais, possível uso para golpes ou assédio.

**Categorias STRIDE relacionadas:** Information Disclosure.

---

### CA04 — Indisponibilidade durante horário de pico

**Ator:** atacante externo ou grupo de atacantes.

**Objetivo:** impedir que clientes e estabelecimentos usem o sistema durante o horário de maior movimento (ex.: almoço/jantar).

**Condições necessárias:**
- o sistema não limita a quantidade de requisições por usuário/IP;
- a infraestrutura não suporta picos de acesso muito acima do normal;
- não existem mecanismos suficientes de proteção contra sobrecarga (rate limiting, CDN, autoscaling).

**Fluxo de abuso:**
1. O atacante identifica o horário de maior demanda do aplicativo.
2. O atacante envia uma grande quantidade de requisições ao app ou à API.
3. Os recursos do sistema ficam sobrecarregados.
4. Clientes legítimos não conseguem fazer pedidos e estabelecimentos não recebem notificações.
5. O serviço fica total ou parcialmente indisponível durante o período crítico.

**Impacto esperado:** perda de vendas para os estabelecimentos, frustração dos clientes, prejuízo financeiro e de reputação para a plataforma.

**Categorias STRIDE relacionadas:** Denial of Service.

---

### CA05 — Acesso indevido ao painel administrativo

**Ator:** entregador ou estabelecimento mal-intencionado (usuário autenticado com privilégios limitados).

**Objetivo:** acessar funções administrativas da plataforma para alterar preços, cadastros, permissões de usuários ou dados sensíveis de outros estabelecimentos/entregadores.

**Condições necessárias:**
- o sistema não valida corretamente o papel (role/perfil) do usuário em cada requisição;
- parâmetros de permissão são enviados pelo cliente (ex.: `?role=admin`) e confiados pelo backend;
- não há revisão ou logs de auditoria para ações administrativas;
- endpoints administrativos não estão isolados em rede ou com autenticação em múltiplos fatores.

**Fluxo de abuso:**
1. O atacante acessa o sistema com suas credenciais comuns (entregador ou estabelecimento).
2. O atacante identifica uma URL ou parâmetro de requisição destinado a administradores (ex.: `/admin/usuarios`, `/admin/precos`).
3. O atacante modifica a requisição para incluir um parâmetro como `role=admin` ou utiliza um token de acesso de baixo privilégio para chamar a API administrativa.
4. O sistema, por falha de autorização, processa a requisição e retorna os dados ou permite as alterações.
5. O atacante altera preços de produtos, desativa concorrentes, visualiza dados financeiros de outros estabelecimentos ou concede permissões indevidas a si mesmo.

**Impacto esperado:** fraude financeira (alteração de preços), roubo de dados sensíveis de toda a plataforma, desconfiguração de cadastros, perda de confiança de clientes e parceiros, necessidade de rollback manual e correção emergencial.

**Categorias STRIDE relacionadas:** Elevation of Privilege.

---

### CA06 — Manipulação de valores e itens no carrinho antes da confirmação

**Ator:** atacante externo (interceptação de rede) ou usuário autenticado malicioso.

**Objetivo:** reduzir o valor total do pedido, adicionar itens gratuitos ou aplicar descontos indevidos, causando prejuízo ao estabelecimento ou à plataforma.

**Condições necessárias:**
- o sistema não recalcula o valor total no backend a partir dos itens e preços vigentes;
- a comunicação entre cliente e servidor não utiliza criptografia adequada (ex.: falta de HTTPS) ou a API confia cegamente nos valores enviados pelo cliente;
- não há assinatura digital ou hash de integridade no payload da requisição;
- o servidor não valida limites mínimos de preço ou quantidade de itens.

**Fluxo de abuso:**
1. O atacante inicia o processo de fechamento de um pedido legítimo.
2. O atacante intercepta a requisição HTTP enviada ao backend (ou utiliza ferramentas de desenvolvedor do navegador para modificar o corpo da requisição).
3. O atacante altera o campo `valorTotal` para um valor bem inferior (ex.: R$ 100,00 para R$ 10,00) ou modifica a lista de itens para incluir produtos caros sem alterar o preço final.
4. O atacante envia a requisição adulterada.
5. O backend processa o pedido com os dados falsificados, gerando uma ordem de pagamento com valor incorreto.
6. O pagamento é aprovado pelo valor menor, e o estabelecimento prepara o pedido (com prejuízo).

**Impacto esperado:** prejuízo financeiro direto ao estabelecimento e à plataforma, distorção de estoque e custos operacionais, necessidade de estorno e retrabalho, possível exploração em larga escala via scripts automatizados.

**Categorias STRIDE relacionadas:** Tampering.

---

### CA07 — Cliente nega recebimento do pedido mesmo tendo recebido

**Ator:** cliente mal-intencionado.

**Objetivo:** obter reembolso integral ou novo envio do pedido, ficando com o produto e o dinheiro (ou com dois produtos pelo preço de um).

**Condições necessárias:**
- o sistema não exige comprovante de entrega confiável (foto, assinatura digital, código de confirmação enviado por SMS);
- a política de reembolso é automatizada e baseada apenas na alegação do cliente;
- os logs do sistema não são imutáveis ou não registram geolocalização do entregador no momento da entrega;
- não há validação cruzada com o tempo de deslocamento ou com a confirmação do estabelecimento.

**Fluxo de abuso:**
1. O cliente faz um pedido normalmente e efetua o pagamento.
2. O entregador realiza a entrega no endereço correto, e o cliente recebe o pedido fisicamente.
3. O cliente acessa o aplicativo ou contata o suporte e alega que o pedido não foi entregue.
4. O cliente solicita reembolso ou novo envio, afirmando que o entregador marcou como entregue indevidamente.
5. O sistema, sem evidências robustas para refutar a alegação, aprova o reembolso ou reenvia o pedido.
6. O cliente fica com o pedido original e recebe o dinheiro de volta (ou um segundo pedido).

**Impacto esperado:** prejuízo financeiro recorrente para a plataforma e estabelecimentos, aumento de custos com frete e produção, desgaste da relação com entregadores, necessidade de equipe dedicada para análises manuais de fraude.

**Categorias STRIDE relacionadas:** Repudiation.

---

### CA08 — Estabelecimento nega recebimento do pedido para não prepará-lo

**Ator:** estabelecimento mal-intencionado.

**Objetivo:** cancelar a comanda sem justificativa, evitar o preparo do pedido (por falta de insumos, preguiça ou por não querer arcar com comissões) sem que a plataforma possa responsabilizá-lo, mantendo eventuais pagamentos ou evitando multas.

**Condições necessárias:**
- o sistema não possui logs confiáveis e com timestamp da notificação enviada ao estabelecimento;
- o estabelecimento pode marcar o pedido como "não recebido" ou "indisponível" sem passar por auditoria;
- não há confirmação em duas vias (ex.: o estabelecimento deve clicar em "receber pedido" dentro de um prazo, e o sistema registra essa ação);
- as notificações (push, e-mail, impressão) não são rastreáveis ou não geram comprovante de entrega da mensagem.

**Fluxo de abuso:**
1. O cliente realiza um pedido e o pagamento é aprovado pela plataforma.
2. O sistema envia a notificação do pedido para o estabelecimento (via aplicativo, impressora térmica ou e-mail).
3. O estabelecimento, de má-fé, alega que não recebeu a notificação ou que o sistema falhou ao registrar o pedido.
4. O estabelecimento não prepara o pedido e, após um tempo, solicita o cancelamento manual.
5. O sistema, sem evidências de que a notificação foi entregue ou visualizada, cancela o pedido e inicia o reembolso ao cliente.
6. O cliente fica frustrado, a plataforma perde a venda e o estabelecimento evita o trabalho ou a comissão sem sofrer penalidades.

**Impacto esperado:** perda de receita da plataforma, clientes insatisfeitos que migram para concorrentes, reputação do estabelecimento abalada (mesmo que de forma indireta), necessidade de auditoria manual e criação de mecanismos de prova de entrega de notificações.

**Categorias STRIDE relacionadas:** Repudiation.
