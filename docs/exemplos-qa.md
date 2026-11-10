# Exemplos práticos de QA

Material didático do portfólio de Abner Evangelista Lins.

Os cenários de negócio, contratos de pedidos e telas de perfil desta página são ilustrativos. O exemplo de Cypress consulta a API pública real do GitHub. O exemplo de Playwright deve ser adaptado a uma aplicação de homologação com os elementos descritos.

## 1. Caso de teste: dois compradores e a última unidade

| Campo | Especificação |
| --- | --- |
| ID | CT-ESTOQUE-001 |
| Objetivo | Verificar a consistência do estoque em duas compras concorrentes. |
| Pré-condições | Produto com uma unidade; dois usuários autenticados; nenhum pedido anterior reservando esse item. |
| Ação | Enviar duas solicitações de compra para a mesma unidade, de forma concorrente, por uma barreira de sincronização na automação. |
| Resultado esperado | Uma compra confirmada; outra recusada por falta de estoque; estoque final igual a zero. |
| Evidências a coletar | Identificadores dos pedidos, respostas das duas requisições e nova consulta do estoque. |

Para este contrato fictício, a compra confirmada retorna HTTP 201 e a tentativa sem estoque retorna HTTP 409. Esses códigos devem ser conferidos na especificação da API usada no projeto real.

```gherkin
Funcionalidade: Reserva da última unidade

  Cenário: Dois compradores disputam a última unidade
    Dado que existe apenas uma unidade disponível do produto
    E dois usuários estão autenticados
    Quando ambos tentam confirmar a compra ao mesmo tempo
    Então apenas um pedido deve ser confirmado
    E a outra tentativa deve informar indisponibilidade
    E o estoque final deve ser zero
```

**Cuidados na investigação:** dois cliques sequenciais não garantem concorrência. É necessário controlar a simultaneidade e verificar o estado final, além das mensagens da interface.

## 2. Matriz de testes de API

Contrato fictício de criação de pedidos: `POST /api/orders`.

| Condição | Resposta esperada neste contrato | Verificação adicional |
| --- | --- | --- |
| Usuário autorizado, produto disponível e quantidade válida | 201 Created | Conferir identificador, quantidade e persistência do pedido. |
| Token ausente ou inválido | 401 Unauthorized | Confirmar que o pedido não foi criado. |
| Usuário autenticado sem permissão | 403 Forbidden | Validar autorização e ausência de efeitos indevidos. |
| Quantidade zero, negativa ou fracionária | 422 Unprocessable Content | Conferir o campo apontado no erro e a ausência de pedido. |
| Estoque insuficiente | 409 Conflict | Conferir saldo e ausência de reserva indevida. |
| Falha interna ou timeout | Erro conforme o contrato | Consultar o estado da operação antes de reenviar para evitar duplicidade. |

Um código de sucesso deve ser acompanhado pela validação do resultado de negócio. Uma resposta de erro, isoladamente, também não prova que a operação deixou de produzir efeitos.

## 3. Cypress: consultar meus repositórios públicos

Exemplo de teste de API somente de leitura. Para utilizá-lo, crie um projeto Cypress, salve o trecho em um arquivo de teste e mantenha acesso à internet. A consulta está sujeita aos limites e à disponibilidade do GitHub.

```javascript
describe('GitHub — repositórios públicos de Abnerlins123', () => {
  it('retorna uma lista de projetos públicos do perfil', () => {
    cy.request({
      method: 'GET',
      url: 'https://api.github.com/users/Abnerlins123/repos',
      qs: { type: 'owner', per_page: 100 },
      headers: {
        Accept: 'application/vnd.github+json',
        'X-GitHub-Api-Version': '2026-03-10',
      },
      failOnStatusCode: false,
    }).then((response) => {
      expect(response.status).to.eq(200);
      expect(response.body).to.be.an('array');

      response.body.forEach((repository) => {
        expect(repository.id).to.be.a('number');
        expect(repository.name).to.be.a('string').and.not.be.empty;
        expect(repository.private).to.eq(false);
        expect(repository.owner.login.toLowerCase())
          .to.eq('abnerlins123');
      });
    });
  });
});
```

**O que esse exemplo demonstra:** leitura de API, verificação de status e validação de campos e visibilidade. A consulta cobre a primeira página, com até 100 resultados. Uma suíte que precise listar todos os projetos deve tratar a paginação.

## 4. Playwright: confirmar a persistência após salvar

Exemplo para uma aplicação fictícia de homologação. Configure a `baseURL`, prepare uma sessão autenticada e uma conta descartável de teste. A página `/perfil` deve oferecer um campo com o rótulo “Nome”, um botão “Salvar” e uma mensagem com `role="status"`.

```javascript
import { test, expect } from '@playwright/test';

test('mantém o nome salvo depois de atualizar a página', async ({ page }) => {
  await page.goto('/perfil');

  const nameField = page.getByLabel('Nome', { exact: true });
  await nameField.fill('Abner Lins');

  await page.getByRole('button', { name: 'Salvar', exact: true }).click();
  await expect(page.getByRole('status')).toContainText('Dados salvos');

  await page.reload();
  await expect(page.getByLabel('Nome', { exact: true }))
    .toHaveValue('Abner Lins');
});
```

**Critério de preparação:** a conta deve começar com um nome diferente de “Abner Lins”. Restaure os dados de teste entre execuções para que um valor já gravado não esconda uma falha de salvamento.

**O que esse exemplo demonstra:** localizar elementos por rótulo e papel, usar asserções que aguardam o resultado e conferir a persistência por uma nova leitura da página.

## 5. Bug report: confirmação de salvamento sem persistência

Modelo ilustrativo de relato, sem execução ou evidências anexadas.

| Campo | Conteúdo |
| --- | --- |
| ID | BUG-PERFIL-001 |
| Título | Nome volta ao valor anterior após confirmação de salvamento e recarga da página. |
| Ambiente | Homologação — informar a versão da aplicação, o navegador e a data da execução. |
| Pré-condição | Usuário autenticado; nome atual diferente do nome usado no teste. |
| Resultado esperado | O novo nome permanece ao atualizar a página. |
| Resultado observado no cenário | A tela confirma o salvamento, mas a nova leitura exibe o nome anterior. |
| Impacto | O usuário recebe uma confirmação incorreta e perde a alteração. |
| Severidade sugerida | Média neste exemplo; reavaliar conforme o dado afetado e o impacto real. |
| Evidências a anexar | Requisição de gravação, resposta da leitura posterior, captura ou vídeo e identificador da versão. |

**Passos para reproduzir:**

1. Acessar a página de perfil com a conta de teste.
2. Alterar o campo Nome para um valor diferente do atual.
3. Selecionar Salvar.
4. Conferir a mensagem de confirmação.
5. Recarregar a página.
6. Comparar o nome exibido com o valor enviado.

## 6. Reteste e regressão após a correção

Exemplo de mudança: a gravação do perfil passa a aguardar a persistência e invalidar o cache.

| Teste selecionado | Classificação | Motivo |
| --- | --- | --- |
| Salvar o nome e conferir após uma nova leitura | Reteste | Repete o defeito que motivou a correção. |
| Alterar outro campo que usa a mesma camada de gravação | Regressão | Verifica um fluxo que pode ter sido afetado pela mudança. |
| Simular falha de gravação e conferir a mensagem | Regressão | Verifica o tratamento de erro do fluxo alterado. |

A seleção deve acompanhar o risco e o escopo da mudança. A execução deve registrar dados usados, versão testada, resultados e evidências.

## Referências técnicas

- [Cypress: cy.request](https://docs.cypress.io/api/commands/request)
- [Playwright: asserções](https://playwright.dev/docs/test-assertions)
- [Playwright: localização de elementos](https://playwright.dev/docs/locators)
- [GitHub: listar repositórios públicos de um usuário](https://docs.github.com/en/rest/repos/repos#list-repositories-for-a-user)

[Voltar ao perfil](../README.md).
