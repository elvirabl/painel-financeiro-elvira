# Painel Financeiro da Elvira — RR Resistências Industriais

Aplicação independente de custos, compras, formação de preços e contas a pagar. A calculadora técnica não é modificada ou acessada.

## Dados preservados e teclado

A limpeza automática foi desativada. Cadastros, notas e orçamentos salvos ficam no localStorage deste navegador e permanecem após recarregar. Primeiro acesso sem base salva começa vazio. Não há compartilhamento automático entre navegadores ou máquinas. Exporte backups regularmente; dados já perdidos na versão temporária só podem ser recuperados por um backup existente.

**Enter avança para o próximo campo do mesmo formulário e não envia o formulário.** Depois do último campo, retorna ao primeiro. Use os botões para salvar ou confirmar. Texto multilinha mantém Enter como quebra de linha. Campos ainda não salvos são preservados durante a navegação pelos módulos da página; um recarregamento descarta esses rascunhos.

O menu lateral tem rolagem independente para alcançar todos os módulos em telas menores.

## Instalação e execução

Requer Node.js 20+ e navegador moderno.

1. Execute `npm install` para a dependência de testes XML.
2. Execute `npm start` ou `node server.mjs`.
3. Abra http://127.0.0.1:4173 .
4. Execute `npm test`.
5. /xml-tests.html contém testes adicionais do DOMParser nativo. /nfe-demonstracao.xml é uma nota fictícia.

A aplicação não precisa de bibliotecas no navegador nem compilação. @xmldom/xmldom é somente de desenvolvimento.

## Editar produtos, materiais e fornecedores

Em **Materiais e fornecedores**, clique em **Editar material**. Código, descrição, categoria, fornecedor, unidade de compra, unidade de consumo, conversão e novo custo proposto podem ser alterados. Informe o responsável e clique em **Salvar material**. O ID interno é preservado para manter os vínculos. Código é único por fornecedor.

O novo custo gera uma proposta: ele não substitui o custo aprovado até a decisão em **Aprovação de custos**. Trocar a unidade de consumo exige informar um custo na unidade nova e decidir primeiro qualquer proposta pendente; o material fica sem custo utilizável até a nova aprovação. Isso evita aplicar um preço por metro como se fosse por centímetro.

**Editar fornecedor** permite corrigir nome e documento sem perder o vínculo dos materiais. O histórico das notas mantém o fornecedor original do documento. As edições registram responsável e valores anteriores na auditoria local.

Em **Custos e preços**, **Editar produto / criar revisão** reapresenta as dimensões, elementos, consumos, acessórios, rateios, tributos e margem de um orçamento salvo. Calcular salva outra versão usando os custos aprovados atuais; a versão anterior permanece preservada.

## Importação XML independente

**Importar XML**, **Lançamento manual** e **Compras registradas** têm telas e formulários separados. XML não depende de cadastro nem lançamento manual prévio.

O painel lê nome e CNPJ/CPF do fornecedor; código do produto com zeros; descrição; unidade; quantidade; preço unitário; frete; seguro; descontos; demais despesas e impostos. Fornecedores são identificados pelo documento; materiais são vinculados por fornecedor + código, com opção de vínculo manual. Escolha Matéria-prima, Acessório ou Componente e confira o fator de conversão, por exemplo barra de 6 metros para consumo em metros = 6. A importação cria os cadastros automaticamente ao confirmar.

## Editar itens de notas

Antes de importar, abra **Editar campos dos produtos desta nota**. Código, descrição, unidade de compra, quantidade, preço unitário, total do item, desconto, frete, seguro, outras despesas e valores dos impostos são editáveis. Clique em **Salvar campos e conferir totais** e depois importe.

Para nota já registrada, em **Compras registradas** clique em **Editar itens da nota**. Edite os campos, confira as categorias e conversões, informe o responsável e salve a edição. A nota mantém sua chave, valor total e pagamentos. Todos os custos da nota voltam para aprovação conjunta. Propostas pendentes da versão anterior são marcadas como substituídas, sem apagar sua auditoria.

O XML integral e os itens originais ficam preservados. As edições representam dados de trabalho para conferência, não uma alteração do documento fiscal. Quantidade × preço unitário deve conferir com o total do item; a composição continua obrigada a fechar com os totais originais da nota. Diferenças bloqueiam a importação ou revisão.

Quando despesas já estiverem distribuídas nos itens e fecharem com os totais, a distribuição original é mantida. Se houver apenas um total global, o rateio usa o valor líquido dos produtos e fecha centavos por maiores frações. Distribuições parciais ou divergentes exigem conferência. ICMS, PIS, COFINS e tributos aproximados não são somados novamente ao preço. IPI, II, ICMS-ST, FCP-ST, IPI devolvido e desoneração são tratados na composição conforme o XML. A conferência não substitui validação fiscal e revisão contábil.

Duplicatas de cobr/dup mantêm datas e valores exatos e devem somar o total da nota. Sem vencimento no XML, informe a data confirmada. Dados de pag/detPag são preservados e não geram quitação bancária automática. A chave impede nota duplicada. Desmarcar item exclui apenas cadastro/atualização de material; financeiro permanece pelo total integral.

## Aprovação e contas a pagar

Aprovação da nota abrange seu valor total e todos os custos vinculados, com responsável e confirmação. Materiais repetidos são agrupados por média ponderada das quantidades de consumo. Aprovar notas não altera orçamentos anteriores. Pagamentos parciais ou totais são registrados separadamente e não alteram custos. Parcelas manuais são mensais, com centavos preservados e ajuste ao último dia disponível do mês; parcelas XML usam as datas do documento.

**Lançamento manual:** referência documental opcional. Informe fornecedor, material, documentação, emissão, recebimento, conversão, valores e vencimentos.

## Custos de produção e preços

Cadastre salário, provisões de 13º e férias, encargos agregados incluindo FGTS e horas produtivas. Custo/hora = salário × (1 + provisões) × (1 + encargos) / horas produtivas. Etapas usam minutos por bloco iniciado de 100 mm por elemento, mesma referência entre diâmetros. Valide essa interpretação com os tempos reais da RR.

Informe dimensões finais após compactação e consumos técnicos sem geometria presumida. Bases de consumo: conjunto, por elemento ou por metro de elemento. Cabeçotes e flanges são conjuntos completos com solda incluída. Terminais têm quantidade informada; nipples sugerem duas unidades por elemento, ajustáveis.

Indiretos de fabricação e administrativo são valores atribuídos em reais, evitando incluir custos já presentes nas outras rubricas. Tributos de venda e margem são configurados pela RR. Preço por margem = custo / (1 − tributos − margem); preço por markup = custo × multiplicador. Margem bruta deduz materiais, acessórios, mão de obra e fabricação; a líquida estimada deduz também administrativo e tributos. Desconto mostra impacto no resultado.

## Relatórios e backups

Três últimos meses completos por padrão. Entrada usa recebimento; compromissos usam vencimentos; quitações usam a data do pagamento. Início inclusivo e fim exclusivo; comparação com período anterior de igual duração em dias. Excel usa HTML tabular em .xls; PDF usa impressão do navegador.

**Exportar backup** gera JSON. Em **Configurações**, restaurar backup substitui a base após confirmação. Demonstração é carregada somente em base vazia. Backup contém os dados locais; ele não é enviado ao GitHub.

## Organização e validação

- dist/domain.mjs: custos, preços, aprovações e parcelas.
- dist/nfe.mjs: NF-e, conciliação, rateio e cadastro automático.
- dist/editing.mjs: edição de materiais, fornecedores e itens, revisão e auditoria.
- dist/keyboard.mjs: Enter para avançar entre campos.
- dist/store.mjs: repositório local persistente, substituível por API/banco futuro.
- dist/app.mjs: interface, rascunhos e coordenação.
- technical-input.schema.json: contrato de integração técnica futura, ainda não conectada.

42 testes automatizados cobrem domínio, XML, conciliação, pagamentos, edição, aprovação, teclado, persistência e telas. Verificado no navegador: Enter muda foco sem enviar; rascunhos sobrevivem à navegação; material editado permanece após recarregar; nota editada mantém total e originais; menu usa rolagem própria.

## Limites

Sistema inicial de testes, sem autenticação de operadores, banco compartilhado, conciliação bancária ou validação SEFAZ. Auditoria local não é imutável. Backups têm validação estrutural básica; use arquivos confiáveis. Fórmulas fiscais não suportadas são bloqueadas se a composição não fechar. Ainda pendentes: edição individual de vencimentos, rateios mensais automáticos, XLSX/PDF nativos e comparação por meses de calendário.
