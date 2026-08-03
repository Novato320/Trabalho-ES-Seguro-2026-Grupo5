## 5. Modelagem de ameaças com STRIDE

| ID | Categoria STRIDE | Componente ou ativo | Ameaça identificada | Possível impacto |
| --- | --- | --- | --- | --- |
| T01 | Spoofing | Conta do estudante | Um atacante utiliza credenciais roubadas para acessar a conta de um estudante | Solicitação ou cancelamento de matrículas em nome da vítima |
| T02 | Tampering | Solicitações e banco de dados | Um usuário altera indevidamente o componente, o resultado, o horário ou o número de vagas | Matrículas incorretas, favorecimento indevido e inconsistência acadêmica |
| T03 | Repudiation | Solicitações, cancelamentos e logs | Um usuário ou servidor nega ter realizado uma operação e o sistema não possui registros confiáveis | Impossibilidade de responsabilização e dificuldade para resolver contestações |
| T04 | Information Disclosure | Dados pessoais e acadêmicos | Uma pessoa acessa históricos, resultados ou listas de estudantes sem autorização | Violação de privacidade e exposição de informações acadêmicas |
| T05 | Denial of Service | Portal, autenticação ou API | Um atacante envia uma grande quantidade de requisições durante o período de matrículas | Indisponibilidade do sistema e perda de prazos pelos estudantes |
| T06 | Elevation of Privilege | Controle de acesso | Um estudante explora uma falha de autorização e obtém permissões de secretaria ou administrador | Alteração de ofertas, vagas, permissões e matrículas de outros estudantes |

### 5.1 Interpretação da análise

As ameaças demonstram que diferentes partes do sistema precisam ser protegidas. As contas estão relacionadas à identidade dos usuários; as solicitações e vagas dependem da integridade dos dados; os logs permitem responsabilizar os autores das operações; os dados acadêmicos exigem confidencialidade; o portal precisa permanecer disponível; e as funções administrativas devem ser acessíveis somente por usuários autorizados.
