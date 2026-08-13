# Etapa 5 — Verificação de Vulnerabilidades

## 1. Sistema ou ambiente testado

A verificação foi realizada em uma instância local do OWASP Juice Shop,
aplicação deliberadamente vulnerável destinada ao treinamento e estudo
de segurança em aplicações web.

## 2. Ferramenta utilizada

Foi utilizado o OWASP ZAP para realizar a verificação de segurança da
aplicação.

## 3. Configuração do teste

O OWASP Juice Shop foi executado localmente e disponibilizado em
`http://localhost:3000`.

A verificação foi realizada utilizando os mecanismos de varredura do
OWASP ZAP, incluindo descoberta das páginas da aplicação e varredura ativa.

## 4. Evidências da execução

As capturas de tela e o relatório gerado pelo OWASP ZAP estão armazenados
em:

`evidencias/etapa-5/`

## 5. Análise dos alertas

### A01 — Possível Injeção SQL

#### Identificação

O resultado de maior criticidade encontrado pelo OWASP ZAP foi um possível
caso de **SQL Injection**, classificado como:

- **Risco:** Alto
- **Confiança:** Baixa
- **CWE:** CWE-89
- **Parâmetro afetado:** `q`
- **Endpoint:** `/rest/products/search`
- **Método:** GET

O alerta foi produzido pelo scanner ativo do OWASP ZAP durante a realização
da varredura.

#### Evidência técnica

Durante o teste, o ZAP enviou o seguinte valor ao parâmetro de pesquisa `q`:

`'(`

A requisição provocou a seguinte resposta da aplicação:

`HTTP/1.1 500 Internal Server Error`

Além do erro HTTP, a aplicação retornou informações internas do banco de
dados, incluindo:

`SQLITE_ERROR: near "(": syntax error`

A resposta também apresentou a consulta SQL que estava sendo processada,
indicando que o valor fornecido pelo parâmetro `q` influenciou a construção
da consulta executada pela aplicação.

Esse comportamento constitui um indício relevante de tratamento inseguro
da entrada recebida pelo endpoint. Entretanto, como o próprio OWASP ZAP
classificou o alerta com **confiança baixa**, o resultado da varredura deve
ser tratado como uma **possível vulnerabilidade de SQL Injection que exige
validação adicional**, e não como comprovação definitiva de exploração.

#### Impacto potencial

Caso uma vulnerabilidade de SQL Injection seja confirmada, um atacante
poderia manipular entradas enviadas à aplicação para interferir nas
consultas realizadas no banco de dados.

Dependendo das permissões disponíveis e da forma como as consultas são
construídas, isso poderia resultar em:

- acesso não autorizado a informações;
- exposição de dados de usuários;
- alteração ou exclusão de registros;
- comprometimento da integridade das informações;
- obtenção de informações internas sobre a estrutura do banco.

Além disso, independentemente da confirmação da SQL Injection, a resposta
observada apresenta outro problema de segurança: detalhes internos do banco
e da consulta SQL são devolvidos ao cliente. Esse comportamento fornece
informações técnicas que podem auxiliar um atacante na realização de novos
testes contra a aplicação.

#### Relação com o sistema de delivery

Em um sistema de delivery, uma vulnerabilidade desse tipo teria impacto
especialmente relevante em funcionalidades que consultam dados a partir de
entradas fornecidas pelos usuários, como busca de produtos, pedidos,
estabelecimentos e contas.

Caso entradas fossem incorporadas de maneira insegura às consultas ao banco,
dados sensíveis do sistema poderiam ficar expostos ou ser manipulados.

O achado se relaciona principalmente aos riscos de exposição e manipulação indevida de dados considerados nas etapas anteriores, especialmente aos objetivos de proteção de confidencialidade e integridade do sistema.

#### Mitigação recomendada

Como medidas de mitigação, recomenda-se:

- utilizar consultas parametrizadas (*prepared statements*);
- não construir comandos SQL por concatenação direta de entradas do usuário;
- realizar validação de dados no backend;
- aplicar o princípio do menor privilégio à conta utilizada pela aplicação
  para acessar o banco de dados;
- tratar exceções internamente, evitando retornar mensagens do banco,
  consultas SQL ou detalhes de implementação ao cliente;
- registrar tentativas e erros relevantes para posterior monitoramento.

Essas medidas reduzem tanto a possibilidade de manipulação das consultas
quanto a quantidade de informações internas disponibilizadas a possíveis
atacantes.

### A02 — A definir

Análise sob responsabilidade de outro integrante do grupo.

### A03 — Cabeçalho Anti-Clickjacking Ausente

#### Identificação

O OWASP ZAP identificou a ausência de proteção contra clickjacking nas
respostas da aplicação, classificada como:

- **Risco:** Médio
- **Confiança:** Média
- **CWE:** CWE-1021
- **Parâmetro afetado:** `x-frame-options`
- **Endpoint (exemplo):** `/socket.io/`
- **Método:** POST

O alerta foi produzido pelo scanner passivo do OWASP ZAP, que identificou
2 ocorrências desse problema no site `http://localhost:3000` durante a
sessão de varredura.

#### Evidência técnica

A requisição de exemplo capturada pelo ZAP foi:

```
POST http://localhost:3000/socket.io/?EIO=4&transport=polling&t=P_uJ01u&sid=SY454IND7UORI0hXAAAC HTTP/1.1
Host: localhost:3000
```

A resposta correspondente não incluiu nenhum dos cabeçalhos de proteção
contra enquadramento:

```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: http://localhost:4200
Vary: Origin
Content-Type: text/html
Content-Length: 2
```

Não há `X-Frame-Options` nem `Content-Security-Policy` com a diretiva
`frame-ancestors` na resposta, confirmando que a aplicação não impede que
suas páginas sejam carregadas dentro de um `<iframe>` de terceiros.

#### Impacto potencial

Sem esse cabeçalho, um atacante pode incorporar a aplicação dentro de um
`<iframe>` invisível em um site malicioso e sobrepor elementos enganosos
sobre a interface real. Isso caracteriza um ataque de **clickjacking**: o
usuário acredita estar clicando em um elemento do site do atacante, mas na
verdade está interagindo com a aplicação vulnerável carregada por trás.

Dependendo da ação induzida, isso pode resultar em:

- execução de ações não intencionais em nome do usuário autenticado;
- alteração de dados de conta sem consentimento;
- confirmação indevida de pedidos ou pagamentos;
- exposição do usuário a fraudes que dependem de sua sessão ativa.

#### Relação com o sistema de delivery

Em um sistema de delivery, ações sensíveis como confirmar um pedido,
alterar um endereço de entrega ou aprovar um pagamento poderiam ser
induzidas por meio de clickjacking caso o usuário esteja autenticado e
acesse, sem saber, uma página maliciosa que embuta a aplicação.

O achado se relaciona aos riscos de manipulação indevida de ações do
usuário e comprometimento da integridade de operações consideradas nas
etapas anteriores do trabalho.

#### Mitigação recomendada

Como medidas de mitigação, recomenda-se:

- configurar o cabeçalho `X-Frame-Options: DENY` (ou `SAMEORIGIN`, caso
  algum fluxo legítimo precise de frame do próprio domínio);
- ou, de forma mais moderna, configurar
  `Content-Security-Policy: frame-ancestors 'none'` (ou `'self'`), que
  substitui o `X-Frame-Options` e cobre outros cenários de política de
  segurança de conteúdo;
- aplicar a configuração via middleware central no backend (ex.: `helmet`
  em aplicações Express/Node), garantindo que o cabeçalho seja incluído
  em todas as rotas, e não apenas nas testadas.

| ID | Alerta ou achado | Evidência | Possível impacto | Relação com OWASP ou CWE | Correção proposta |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **A01** | Possível Injeção SQL | O ZAP enviou o payload `'(` no parâmetro `q` do endpoint `/rest/products/search`. A aplicação respondeu `HTTP 500`, retornou `SQLITE_ERROR` e expôs a consulta SQL processada. O alerta foi classificado como risco Alto e confiança Baixa. | Possível acesso, alteração ou exposição indevida de dados caso a vulnerabilidade seja confirmada | **CWE-89 — SQL Injection** | Utilizar consultas parametrizadas (*prepared statements*), evitar concatenação direta de entradas em comandos SQL, validar entradas no servidor e não expor detalhes internos do banco nas respostas de erro |
| **A03** | Cabeçalho Anti-Clickjacking Ausente | Resposta do endpoint `/socket.io/` sem `X-Frame-Options` nem `Content-Security-Policy` com `frame-ancestors`. Risco Médio, confiança Média, 2 ocorrências no site. | Possibilidade de clickjacking: ações induzidas do usuário autenticado (ex.: confirmar pedido/pagamento) via iframe malicioso | **CWE-1021 — Improper Restriction of Rendered UI Layers or Frames** | Configurar `X-Frame-Options: DENY`/`SAMEORIGIN` ou `Content-Security-Policy: frame-ancestors`, aplicado via middleware central no backend |

Como o alerta de SQL Injection apresentou confiança baixa, seria necessária validação adicional para determinar se o comportamento representa uma vulnerabilidade explorável ou um possível falso positivo.

## 6. Limitações da verificação

A análise foi realizada por meio de uma ferramenta automatizada de varredura, portanto os alertas identificados não devem ser interpretados automaticamente como vulnerabilidades confirmadas.

O OWASP ZAP atribui níveis de risco e confiança aos achados, e alguns resultados podem exigir validação manual adicional. Um exemplo observado nesta sessão foi o alerta de possível SQL Injection, classificado como risco Alto, mas com confiança Baixa.

Além disso, a sessão foi realizada sobre uma instância local do OWASP Juice Shop e representa um cenário controlado de treinamento. Os resultados não devem ser generalizados automaticamente para outros sistemas ou ambientes.

Por esse motivo, os achados desta etapa foram tratados como indicadores de possíveis problemas de segurança que devem ser analisados individualmente antes da definição de uma correção ou classificação definitiva.