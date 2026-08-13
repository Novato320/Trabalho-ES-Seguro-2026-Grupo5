## Prática 1 — Controle de autorização por recurso (Resource-Based Authorization)

### 1. Risco e requisito relacionados

| Referência | Descrição |
| :--- | :--- |
| **Risco relacionado** | **R04 — Acesso indevido a dados de outros usuários** (Nível Crítico). Ocorre quando um usuário autenticado manipula identificadores (ex.: ID do pedido na URL) para acessar informações de terceiros. |
| **Requisito de segurança** | **RNF09** — O sistema deve validar no *backend*, em toda requisição, se o recurso solicitado (pedido, endereço, dados de pagamento) pertence ao usuário autenticado, recusando o acesso em caso negativo. |
| **Caso de abuso relacionado** | **CA03** — Consulta indevida de pedidos e dados de outros clientes (manipulação de ID na URL/API). |

---

### 2. Testes definidos antes da implementação (TDD)

Os testes foram escritos **antes** de qualquer pseudocódigo, conforme exigido.

| ID | Tipo | Cenário / Ação do usuário | Condição do sistema | Resultado esperado |
| :--- | :--- | :--- | :--- | :--- |
| **TS-01** | **Caso válido (happy path)** | O **Cliente João**, autenticado com token válido, faz uma requisição para consultar o pedido. | O pedido no banco de dados realmente pertence a João. | O sistema retorna os dados do pedido. Nenhum log de anomalia é gerado. |
| **TS-02** | **Caso malicioso / inválido (IDOR)** | O **Cliente João** faz uma requisição para consultar outro o pedido. | O pedido no banco pertence à Maria, não a João. | O sistema **NÃO** retorna os dados de Maria. Além disso, gera um **log de auditoria** com alerta de tentativa de acesso indevido. |

---

### 3. Implementação / Pseudocódigo da validação no *backend*

Abaixo está a implementação em formato de **pseudocódigo estruturado** (aplicável a qualquer linguagem, como Python/Java/C#). O foco é o middleware ou interceptor que valida a propriedade do recurso **antes** de executar a lógica de negócio.

```python

function validarAutorizacaoRecurso(requisicao, usuario_logado):
    """
    Parâmetros:
    - requisicao: contém o método (GET, PUT, DELETE), a URL e o corpo.
    - usuario_logado: objeto extraído do Token JWT (contém 'id', 'email', 'papel').
    
    Retorno:
    - Se autorizado: permite a continuação da requisição
    - Se não autorizado: interrompe a requisição e retorna erro.
    """

    # Captura o ID sendo procurado (ex: pedidos/324)
    id_recurso = extrair_id_da_url(requisicao.url)
    
    # Captura o TIPO do recurso (ex: "pedidos", "enderecos", "pagamentos")
    tipo_recurso = extrair_tipo_da_url(requisicao.url)

    # Se o recurso não tem "dono", ou se é um recurso público (ex: listagem de estabelecimentos), pula a verificação.
    # Mas para dados sensíveis (pedidos, endereços, pagamentos), a verificação é OBRIGATÓRIA.
    if tipo_recurso in ["pedidos", "enderecos", "pagamentos", "entregas"]:
        
        # Consulta o banco de dados para descobrir o DONO do recurso.
        dono_id = banco_dados.buscar_dono_do_recurso(tipo_recurso, id_recurso)
        
        # em caso de erro, não revela se o ID existe ou não.
        se dono_id for None:
            retornar_erro(404, "Recurso não encontrado")
            interromper_execucao()

        # Compara o ID do dono (vindo do BD) com o ID do usuário logado (vindo do JWT).
        se dono_id != usuario_logado.id:
            # bloqueia o acesso.
            # Opção: retornar 403 (proibido) ou 404 (recomendado para evitar enumeração).
            retornar_erro(403, "Ação não autorizada") 
            
            # loga a tentativa para detecção de ataques
            logs_auditoria.registrar_evento(
                tipo="TENTATIVA_ACESSO_NAO_AUTORIZADO",
                usuario_id=usuario_logado.id,
                recurso_tipo=tipo_recurso,
                recurso_id=id_recurso,
                ip_origem=requisicao_http.ip,
                timestamp=agora()
            )
            
            # Começa a monitorar o usuario
            monitoramento.contabilizar_anomalia(usuario_logado.id)
            
            interromper_execucao()

    # Se passou pela validação, libera a requisição
    continuar_fluxo_requisicao()

# =====
# EXEMPLO DE USO

endpoint GET /api/pedidos/<id>:
    # Autenticação (já feita pelo gateway)
    usuario_atual = obter_usuario_do_token(request)
    
    validarAutorizacaoRecurso(request, usuario_atual)
    
    #Se chegou aqui, está autorizado.
    pedido = banco_dados.buscar_pedido_por_id(request.param_id)
    retornar_json(200, pedido)
```

---

### 4. Resultado esperado com a implementação

A aplicação desse controle gera os seguintes efeitos práticos no sistema:

1. **Eliminação do IDOR (Insecure Direct Object Reference)**: Mesmo que um atacante (ou um usuário curioso) altere o número do pedido na URL de `/pedidos/101` para `/pedidos/999`, o sistema sempre consultará o banco de dados para verificar o dono. Como a verificação ocorre no servidor, a falha descrita no **CA03** (consulta indevida de pedidos) deixa de existir.

2. **Redução do risco R04**: Com essa validação obrigatória em todos os endpoints sensíveis, o acesso indevido a dados de terceiros se torna inviável.

3. **Rastreabilidade e dissuasão**: O log gerado na tentativa de acesso inválido (TS-02) permite que a equipe de segurança identifique tentativas de ataque em andamento (funcão *Detect* do NIST CSF) e tome ações como bloquear o IP ou revogar a sessão do usuário malicioso.

---

### 5. Referências de Segurança

| Referência | Aplicação no controle |
| :--- | :--- |
| **OWASP Authorization Cheat Sheet** | Seção *"Authorization Checks"* – recomenda que todas as decisões de autorização sejam tomadas no servidor, baseadas em dados do servidor (nunca no cliente). |
| **OWASP ASVS v4.0.3 – V4 Access Control** | **4.1.3**: Verifica se os controles de acesso são aplicados no servidor para cada requisição a recursos. <br> **4.2.1**: Garante que os usuários só acessem recursos que lhes pertencem (verificação de propriedade). |
| **OWASP Top 10 2021 – A01:2021** | Mapeamento direto para *Broken Access Control* – a prática mitiga especificamente a categoria **CWE-639: Authorization Bypass Through User-Controlled Key** (IDOR). |
