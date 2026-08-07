## 5. Modelagem de ameaças com STRIDE

| ID  | Categoria STRIDE       | Componente ou ativo              | Ameaça identificada | Possível impacto |
|-----|------------------------|-----------------------------------|----------------------|-------------------|
| T01 | Spoofing               | Conta do cliente/entregador       | Um atacante utiliza credenciais roubadas para acessar a conta de outro usuário | Acesso a dados pessoais, realização de pedidos ou pagamentos em nome da vítima |
| T02 | Tampering              | Pedido / carrinho de compras      | Um usuário altera indevidamente o valor, os itens ou o endereço de entrega de um pedido antes da confirmação | Prejuízo financeiro ao cliente ou ao estabelecimento, entrega em local incorreto |
| T03 | Repudiation            | Confirmações de entrega / logs    | Um entregador ou cliente nega ter realizado (ou recebido) uma entrega, e o sistema não possui registros confiáveis | Impossibilidade de responsabilizar as partes, disputas sobre pagamentos e reembolsos |
| T04 | Information Disclosure | Dados pessoais e de pagamento     | Um usuário acessa endereços, telefones ou histórico de pedidos de outro cliente sem autorização | Violação de privacidade, exposição de dados sensíveis, possível uso fraudulento |
| T05 | Denial of Service      | API / backend de pedidos          | Um atacante envia um grande volume de requisições durante o horário de pico (ex.: jantar), sobrecarregando o sistema | Indisponibilidade do app, clientes não conseguem pedir, estabelecimentos perdem vendas |
| T06 | Elevation of Privilege | Controle de acesso / painel admin | Um estabelecimento ou entregador explora falha de autorização e acessa funções reservadas ao administrador | Alteração indevida de preços, cadastros, permissões ou dados de outros usuários |

### 5.1 Interpretação da análise

As ameaças mostram que diferentes partes do sistema precisam de proteção: as contas dependem da identidade dos usuários (clientes, estabelecimentos e entregadores); os pedidos e pagamentos dependem da integridade dos dados; os registros de entrega permitem responsabilizar as partes envolvidas; os dados pessoais e de pagamento exigem confidencialidade; a API precisa permanecer disponível durante picos de demanda; e as funções administrativas devem ser acessíveis somente a usuários autorizados.