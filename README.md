# Abner Evangelista Lins

**QA | Automação de Testes | Testes de API | Segurança da Informação**

Sou profissional de TI com experiência em operações NOC/CCO, monitoramento, tratamento de incidentes e documentação operacional. Estou direcionando minha carreira para Qualidade de Software, conectando a visão de produção à investigação de defeitos e à validação de aplicações.

Curso Segurança da Informação e desenvolvo meus conhecimentos em testes manuais, automação e segurança de aplicações. Busco oportunidades em QA e automação de testes.

## Conhecimentos e estudos

| Área | Ferramentas e práticas |
| --- | --- |
| Qualidade de software | Testes funcionais, exploratórios, smoke, reteste, regressão e casos de teste |
| Automação | Cypress, Playwright e Selenium WebDriver |
| APIs | Postman, HTTP, REST, JSON, autenticação e autorização |
| Linguagens | JavaScript, TypeScript, Python e SQL |
| Versionamento e entrega | Git, GitHub e estudos de GitHub Actions / CI/CD |
| Operações e segurança | Zabbix, SIEM, Linux, gestão de incidentes e runbooks |

Meu foco atual é praticar essas ferramentas em cenários de negócio, documentar os resultados e evoluir a cobertura dos projetos.

## Projetos e prática

| Projeto | Situação | Contexto |
| --- | --- | --- |
| [qa-cypress-api](https://github.com/Abnerlins123/qa-cypress-api) | Em estruturação | Repositório iniciado para organizar estudos de Cypress e testes de API. |
| [webdojo](https://github.com/Abnerlins123/webdojo) | Ambiente acadêmico — fork | Aplicação do curso Ninja do Cypress utilizada como ambiente de estudo. Projeto original de Fernando Papito e equipe. |
| Laboratório QA interativo | Protótipo com acesso privado | Simulação de bugs, respostas de API e decisões de reteste e regressão, com exemplos de Cypress e Playwright. |

O laboratório permanece com acesso restrito. Os exemplos abaixo podem ser consultados diretamente neste repositório.

## Exemplos de raciocínio em QA

| Cenário | O que investigar | Resultado esperado |
| --- | --- | --- |
| Dois usuários compram a última unidade | Concorrência, reserva de estoque e respostas da API | Apenas uma compra confirmada e estoque nunca negativo. |
| A conexão cai durante um pagamento | Estado da transação e reenvio com a mesma chave de idempotência | Uma única cobrança para a mesma operação. |
| A tela confirma o salvamento, mas os dados somem ao atualizar | Persistência, nova leitura e tratamento de falhas | O dado salvo permanece após recarregar a página. |

[Ver exemplos detalhados de casos de teste, bug report, Cypress e Playwright](docs/exemplos-qa.md).

### Como decido entre reteste e regressão

- **Reteste:** repetir o cenário do defeito para confirmar a correção.
- **Regressão:** verificar os fluxos que podem ter sido afetados pela mudança.

Por exemplo: depois de corrigir a reserva de estoque, repito a compra simultânea da última unidade e também verifico uma compra comum e o cancelamento de um pedido.

## Minha contribuição para um time de QA

- Investigar falhas considerando o comportamento da interface, a API e o impacto para o usuário.
- Transformar relatos em passos reproduzíveis, critérios de aceite e evidências.
- Priorizar testes conforme o risco da mudança.
- Usar a experiência em operações para considerar disponibilidade, recuperação e observabilidade.

## Contato

- [LinkedIn](https://www.linkedin.com/in/abnerlins/)
- [GitHub](https://github.com/Abnerlins123)
