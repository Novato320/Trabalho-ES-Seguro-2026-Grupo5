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