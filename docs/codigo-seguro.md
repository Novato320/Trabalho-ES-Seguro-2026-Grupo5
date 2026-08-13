# Etapa 4 — Código Seguro e Testes de Segurança

> Este documento segue a estrutura da Etapa 4 do enunciado. As seções de conteúdo (Prática 1 e Prática 2) estão organizadas como esqueleto, prontas para receber o conteúdo de Guilherme Jaques e Gabriel Ortiz. As seções de **Matriz de Rastreabilidade** e **Checklist de Revisão** são a parte de organização/coerência (Dornelles) e já podem ser preenchidas conforme os riscos, requisitos e decisões das Etapas 2 e 3.

---

## 1. Objetivo

Demonstrar como as decisões de arquitetura da Etapa 3 seriam transformadas em práticas de implementação segura, com testes de segurança definidos **antes** da implementação, seguindo a OWASP Cheat Sheet Series.

---

## 2. Prática 1 — Controle de autorização por recurso (Resource-Based Authorization)

**Responsável:** Guilherme Jaques
**Risco relacionado:** R04
**Requisito de segurança relacionado:** RS02 (ver observação de nomenclatura abaixo)
**Decisão de arquitetura relacionada:** DA02

### 2.1 Risco e requisito

| Campo | Conteúdo |
|---|---|
| Risco (R04) | **R04 — Acesso indevido a dados de outros usuários** (Nível Crítico). Ocorre quando um usuário autenticado manipula identificadores (ex.: ID do pedido na URL) para acessar informações de terceiros. Confirmado na matriz de riscos da Etapa 2: Information Disclosure, falha no controle de autorização e validação inadequada sobre a propriedade do recurso, Probabilidade 3, Impacto 4, Pontuação 12 — Crítico. |
| Requisito de segurança | O sistema deve validar no backend, em toda requisição, se o recurso solicitado (pedido, endereço, dados de pagamento) pertence ao usuário autenticado, recusando o acesso em caso negativo. Guilherme Jaques identificou esse requisito como **RNF09**; a tabela de rastreabilidade da Etapa 3 registra o mesmo requisito como **RS02**. Ver observação de nomenclatura abaixo. |
| Caso de abuso relacionado | CA03 — Consulta indevida de pedidos e dados de outros clientes (manipulação de ID na URL/API). |
| Critério de verificação do requisito | *(A preencher — pendente do texto completo e oficial de RS02 na Etapa 3)* |
| Decisão de arquitetura (DA02) | **Autorização por recurso no backend.** Validar no backend se o usuário autenticado possui autorização sobre o recurso solicitado antes de permitir sua consulta ou alteração, já que restrições apenas no frontend podem ser contornadas manipulando diretamente requisições e IDs. Componente afetado: API/Backend, Serviço de Pedidos e mecanismo de autorização. Resultado esperado: impedir que usuários consultem ou alterem pedidos, endereços, dados pessoais ou dados de pagamento de terceiros. |

> **Observação de nomenclatura (revisão de coerência):** Guilherme Jaques referenciou o requisito como **RNF09**, enquanto a tabela de rastreabilidade da Etapa 3 usa o código **RS02** para o mesmo requisito (mesmo risco R04, mesma decisão DA02, mesma vulnerabilidade OWASP A01:2021/CWE-639). O conteúdo é coerente, mas o código do requisito precisa ser padronizado antes da entrega final — recomenda-se usar RS02 em todo o documento, para manter consistência com a Etapa 3, e ajustar isso no arquivo de origem de Guilherme Jaques.

### 2.2 Testes de segurança (definidos antes da implementação)

| ID | Tipo | Cenário / ação do usuário | Condição do sistema | Resultado esperado |
|---|---|---|---|---|
| TS01 | Válido (happy path) | O cliente João, autenticado com token válido, faz uma requisição para consultar o pedido. | O pedido no banco de dados realmente pertence a João. | O sistema retorna os dados do pedido. Nenhum log de anomalia é gerado. |
| TS02 | Malicioso / inválido (IDOR) | O cliente João faz uma requisição para consultar outro pedido. | O pedido no banco pertence à Maria, não a João. | O sistema não retorna os dados de Maria. Gera um log de auditoria com alerta de tentativa de acesso indevido. |

### 2.3 Implementação, pseudocódigo ou descrição detalhada

Pseudocódigo estruturado do middleware/interceptor que valida a propriedade do recurso antes de executar a lógica de negócio:

```text
função validarAutorizacaoRecurso(requisicao, usuario_logado):
    # Parâmetros:
    # - requisicao: contém o método (GET, PUT, DELETE), a URL e o corpo.
    # - usuario_logado: objeto extraído do Token JWT (contém 'id', 'email', 'papel').
    #
    # Retorno:
    # - Se autorizado: permite a continuação da requisição
    # - Se não autorizado: interrompe a requisição e retorna erro.

    # Captura o ID sendo procurado (ex: pedidos/324)
    id_recurso = extrair_id_da_url(requisicao.url)

    # Captura o TIPO do recurso (ex: "pedidos", "enderecos", "pagamentos")
    tipo_recurso = extrair_tipo_da_url(requisicao.url)

    # Para dados sensíveis (pedidos, endereços, pagamentos, entregas), a verificação é obrigatória.
    se tipo_recurso em ["pedidos", "enderecos", "pagamentos", "entregas"]:

        # Consulta o banco de dados para descobrir o DONO do recurso.
        dono_id = banco_dados.buscar_dono_do_recurso(tipo_recurso, id_recurso)

        # Em caso de erro, não revela se o ID existe ou não.
        se dono_id for None:
            retornar_erro(404, "Recurso não encontrado")
            interromper_execucao()

        # Compara o ID do dono (vindo do BD) com o ID do usuário logado (vindo do JWT).
        se dono_id != usuario_logado.id:
            retornar_erro(403, "Ação não autorizada")

            # Loga a tentativa para detecção de ataques.
            logs_auditoria.registrar_evento(
                tipo="TENTATIVA_ACESSO_NAO_AUTORIZADO",
                usuario_id=usuario_logado.id,
                recurso_tipo=tipo_recurso,
                recurso_id=id_recurso,
                ip_origem=requisicao_http.ip,
                timestamp=agora()
            )

            monitoramento.contabilizar_anomalia(usuario_logado.id)

            interromper_execucao()

    # Se passou pela validação, libera a requisição.
    continuar_fluxo_requisicao()


# Exemplo de uso

endpoint GET /api/pedidos/<id>:
    usuario_atual = obter_usuario_do_token(request)
    validarAutorizacaoRecurso(request, usuario_atual)
    # Se chegou aqui, está autorizado.
    pedido = banco_dados.buscar_pedido_por_id(request.param_id)
    retornar_json(200, pedido)
```

### 2.4 Resultado seguro esperado

- **Eliminação do IDOR (Insecure Direct Object Reference):** mesmo que um atacante altere o número do pedido na URL (ex.: de `/pedidos/101` para `/pedidos/999`), o sistema sempre consulta o banco de dados para verificar o dono. Como a verificação ocorre no servidor, o caso de abuso CA03 deixa de existir.
- **Redução do risco R04:** com essa validação obrigatória em todos os endpoints sensíveis, o acesso indevido a dados de terceiros se torna inviável.
- **Rastreabilidade e dissuasão:** o log gerado na tentativa de acesso inválido (TS02) permite que a equipe de segurança identifique tentativas de ataque em andamento (função Detect do NIST CSF) e tome ações como bloquear o IP ou revogar a sessão do usuário malicioso.

### 2.5 Referência OWASP

| Referência | Aplicação no controle |
|---|---|
| OWASP Authorization Cheat Sheet | Seção "Authorization Checks" — recomenda que todas as decisões de autorização sejam tomadas no servidor, baseadas em dados do servidor (nunca no cliente). |
| OWASP ASVS v4.0.3 — V4 Access Control | 4.1.3: verifica se os controles de acesso são aplicados no servidor para cada requisição a recursos. 4.2.1: garante que os usuários só acessem recursos que lhes pertencem (verificação de propriedade). |
| OWASP Top 10 2021 — A01:2021 | Mapeamento direto para Broken Access Control — a prática mitiga especificamente a categoria CWE-639: Authorization Bypass Through User-Controlled Key (IDOR). Coincide com o mapeamento já registrado na tabela de rastreabilidade da Etapa 3 para R04/RS02/DA02. |

---

## 3. Prática 2 — Reautenticação para operações sensíveis

**Responsável:** Gabriel Ortiz
**Risco relacionado:** R01
**Requisito de segurança relacionado:** RS03
**Decisão de arquitetura relacionada:** DA03

### 3.1 Risco e requisito

| Campo | Conteúdo |
|---|---|
| Risco (R01) | **Spoofing.** Um atacante acessa a conta de um cliente ou entregador utilizando credenciais comprometidas e realiza operações em seu nome. Vulnerabilidade/condição: credenciais comprometidas ou reutilizadas e ausência de verificação adicional para acessos ou operações sensíveis. Probabilidade 3, Impacto 4, Pontuação 12 — **Crítico**. (Confirmado na matriz de riscos da Etapa 2.) |
| Requisito (RS03) | O sistema deverá exigir uma verificação adicional (nova autenticação ou segundo fator) antes de confirmar operações sensíveis, como pagamento, alteração de dados cadastrais ou cancelamento de pedido. *Texto fornecido por Gabriel Ortiz; confirmado como código na tabela de rastreabilidade da Etapa 3 (R01 → RS03 → DA03), mas o texto integral oficial do requisito (seção 18.1) ainda não foi compartilhado.* Vulnerabilidade relacionada: OWASP A07:2021 (Identification and Authentication Failures) / CWE-287 (Improper Authentication). |
| Critério de verificação do requisito | *(A preencher — pendente do texto completo de RS03)* |
| Decisão de arquitetura (DA03) | **Verificação adicional para operações sensíveis.** Exigir nova autenticação ou segundo fator antes da execução de operações sensíveis, pois credenciais comprometidas não devem bastar para permitir operações de maior impacto na conta da vítima. Componente afetado: Serviço de Autenticação e API/Backend. Resultado esperado: reduzir a possibilidade de pagamentos, cancelamentos ou alterações de dados serem realizados por um atacante com credenciais comprometidas. |

### 3.2 Testes de segurança (definidos antes da implementação)

| ID | Tipo | Entrada ou ação | Resultado esperado |
|---|---|---|---|
| TS03 | Válido | Cliente autenticado solicita o cancelamento de seu próprio pedido e conclui corretamente a verificação adicional | O sistema permite o cancelamento do pedido |
| TS04 | Malicioso / inválido | Usuário com sessão autenticada tenta cancelar seu pedido sem concluir a verificação adicional exigida | O sistema recusa a operação, registra a tentativa e mantém o pedido sem alteração |

### 3.3 Implementação, pseudocódigo ou descrição detalhada

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

### 3.4 Resultado seguro esperado

A implementação deverá garantir que uma operação sensível, como o cancelamento de um pedido, somente seja executada quando o usuário possuir autorização sobre o recurso e concluir a verificação adicional exigida.

Caso a verificação adicional não seja realizada ou seja inválida, a operação deverá ser recusada e o pedido deverá permanecer inalterado.

Com isso, mesmo que uma sessão ou credencial seja comprometida, operações de maior impacto exigirão uma etapa adicional de verificação.

### 3.5 Referência OWASP

OWASP Cheat Sheet Series — **Authentication Cheat Sheet**, seções sobre reautenticação para funcionalidades sensíveis e reautenticação após eventos de risco.

---

## 4. Matriz de rastreabilidade (Risco → Requisito → Decisão → Prática → Testes)

> Preencher com base nos documentos das Etapas 2 e 3. Serve para verificar visualmente se a cadeia é coerente de ponta a ponta.

| Risco (Etapa 2) | Requisito (Etapa 3) | Decisão de arquitetura (Etapa 3) | Prática (Etapa 4) | Testes (Etapa 4) | Responsável |
|---|---|---|---|---|---|
| R04 Confirmado (Information Disclosure, Crítico) | RS02 Código confirmado, texto integral pendente | DA02 Confirmado (Autorização por recurso no backend) | Prática 1 — Controle de autorização por recurso | TS01, TS02 | Guilherme Jaques |
| R01 Confirmado (Spoofing, Crítico) | RS03 Código confirmado, texto integral pendente | DA03 Confirmado (Verificação adicional para operações sensíveis) | Prática 2 — Reautenticação para operações sensíveis Conteúdo, testes e pseudocódigo entregues | TS03, TS04 | Gabriel Ortiz |

> **Nota atualizada:** com a tabela de rastreabilidade da Etapa 3, a cadeia **Risco → Requisito → Decisão** está confirmada em nível de código para as duas práticas (R04→RS02→DA02 e R01→RS03→DA03), e as decisões de arquitetura (DA02 e DA03) batem exatamente com o que foi implementado nas Práticas 1 e 2 — nenhuma inconsistência encontrada até aqui. O único ponto em aberto é o **texto oficial e completo dos requisitos RS02 e RS03** (seção 18.1 da Etapa 3, com o "critério de verificação"), que ainda não foi compartilhado; assim que estiver disponível, atualizar as linhas "Critério de verificação do requisito" nas seções 2.1 e 3.1.

---

## 5. Checklist de revisão de coerência (Dornelles)

Usar esta lista para revisar o conteúdo produzido por Guilherme Jaques e Gabriel Ortiz assim que estiver pronto.

### 5.1 Cadeia risco → requisito → decisão → prática
- [ ] O risco citado (R04 / R01) é exatamente o mesmo descrito na Etapa 2, sem reinterpretação?
- [ ] O requisito citado (RS02 / RS03) realmente **mitiga** esse risco (não apenas menciona um tema parecido)?
- [ ] A decisão de arquitetura citada (DA02 / DA03) implementa de fato o requisito, e não outro aspecto do sistema?
- [ ] A prática de código escolhida é a forma concreta de cumprir a decisão de arquitetura?

### 5.2 Testes de segurança
- [ ] Existem exatamente 2 testes por prática: 1 válido e 1 malicioso/inválido/não autorizado?
- [ ] Os testes foram escritos **antes** da implementação (ordem lógica do documento: testes → implementação)?
- [ ] O teste malicioso realmente tenta burlar o controle descrito no requisito (ex.: manipular ID, pular etapa de reautenticação), e não um cenário genérico?
- [ ] O "resultado esperado" de cada teste é específico e verificável (ex.: código de erro, log gerado), não apenas "acesso negado" sem detalhe?

### 5.3 Implementação / pseudocódigo
- [ ] O pseudocódigo/descrição é suficiente para entender exatamente onde e como o controle é aplicado (ex.: no backend, antes de retornar o recurso)?
- [ ] Fica claro que a validação ocorre no servidor (não apenas na interface)?
- [ ] O pseudocódigo cobre os dois cenários de teste (válido e malicioso)?

### 5.4 Resultado esperado
- [ ] O resultado seguro esperado é coerente com o critério de verificação definido na Etapa 3 para o requisito correspondente?
- [ ] Está claro o que acontece em caso de falha (ex.: código HTTP, mensagem, registro em log)?

### 5.5 Referência OWASP
- [ ] A referência aponta para uma Cheat Sheet real e pertinente ao tema (autorização / autenticação)?
- [ ] O link/título foi conferido (não citado de memória)?
- [ ] A referência é citada de forma consistente com a usada na Etapa 3 (evitar usar catálogos diferentes sem justificar)?

> **Observação:** a Etapa 3 mapeou R04/RS02 para **OWASP A01:2021 (Broken Access Control) / CWE-639**, e R01/RS03 para **OWASP A07:2021 (Identification and Authentication Failures) / CWE-287** — essas são referências do **OWASP Top 10**, usadas para catalogar a vulnerabilidade. Já as referências da Etapa 4 (Authorization Cheat Sheet e Authentication Cheat Sheet) são do **OWASP Cheat Sheet Series**, usadas como guia prático de implementação. Isso é esperado e coerente (o enunciado pede catálogos diferentes em cada etapa), mas vale registrar essa distinção no texto final para deixar claro que não é uma inconsistência.

### 5.6 Consistência geral do documento
- [ ] Os IDs (R, RS, DA, TS) seguem a numeração já usada nas etapas anteriores, sem conflito?
- [ ] Não há contradição entre o que foi decidido na Etapa 3 e o que foi implementado aqui na Etapa 4?
- [ ] O documento está no caminho correto do repositório: `docs/etapa-4-codigo-seguro.md`?
- [ ] Pseudocódigos/arquivos de teste, se houver, estão versionados em `codigo/etapa-4/`?

---

## 6. Considerações finais

> *(A preencher após consolidação das duas práticas — síntese sobre a coerência entre riscos, requisitos, decisões e práticas; dificuldades encontradas)*