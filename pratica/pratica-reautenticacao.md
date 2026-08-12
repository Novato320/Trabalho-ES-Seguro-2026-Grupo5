# Prática de código seguro — Reautenticação para operações sensíveis

Esta prática demonstra a aplicação de uma verificação adicional antes da execução de operações sensíveis no sistema de delivery, reduzindo o impacto do uso indevido de uma conta comprometida.

## 1. Risco e requisito relacionados

**Risco relacionado:** R01 — Uso indevido de conta.

**Requisito relacionado:** RS03 — O sistema deverá exigir uma verificação adicional (nova autenticação ou segundo fator) antes de confirmar operações sensíveis, como pagamento, alteração de dados cadastrais ou cancelamento de pedido.

A prática busca impedir que apenas o acesso a uma sessão ou credencial comprometida seja suficiente para executar operações de maior impacto na conta do usuário.

## 2. Testes de segurança definidos antes da implementação

Os testes abaixo foram definidos antes do pseudocódigo da solução, considerando um caso de uso válido e um caso em que a verificação adicional não é concluída.

| Teste | Entrada ou ação | Resultado esperado |
| ----- | --------------- | ------------------ |
| TS03 | Cliente autenticado solicita o cancelamento de seu próprio pedido e conclui corretamente a verificação adicional | O sistema permite o cancelamento do pedido |
| TS04 | Usuário com sessão autenticada tenta cancelar seu pedido sem concluir a verificação adicional exigida | O sistema recusa a operação, registra a tentativa e mantém o pedido sem alteração |

## 3. Pseudocódigo da implementação

```text
função cancelarPedido(usuario, pedido, verificacaoAdicional):

    se pedido.clienteId != usuario.id:
        registrarTentativaNaoAutorizada(usuario, pedido)
        retornar "Acesso negado"

    se verificacaoAdicional != VALIDADA:
        registrarFalhaDeVerificacao(usuario, pedido)
        retornar "Verificação adicional necessária"

    pedido.status = "CANCELADO"

    salvar(pedido)

    retornar "Pedido cancelado com sucesso"
```

O pseudocódigo realiza primeiro a verificação de autorização sobre o pedido. Em seguida, exige que a verificação adicional tenha sido concluída com sucesso antes de permitir o cancelamento.

Caso o pedido não pertença ao usuário autenticado ou a verificação adicional não tenha sido validada, a operação é recusada e o estado do pedido não é alterado.

## 4. Resultado esperado

A implementação deverá garantir que uma operação sensível, como o cancelamento de um pedido, somente seja executada quando o usuário possuir autorização sobre o recurso e concluir a verificação adicional exigida.

Caso a verificação adicional não seja realizada ou seja inválida, a operação deverá ser recusada e o pedido deverá permanecer inalterado.

Com isso, mesmo que uma sessão ou credencial seja comprometida, operações de maior impacto exigirão uma etapa adicional de verificação.

## 5. Referência de segurança

A prática utiliza como referência a **OWASP Authentication Cheat Sheet**, especialmente as recomendações de **reautenticação para funcionalidades sensíveis** e **reautenticação após eventos de risco**.


Referência: OWASP Cheat Sheet Series — Authentication Cheat Sheet.
