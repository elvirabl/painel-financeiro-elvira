# Painel Financeiro da Elvira — RR Resistências Industriais

Aplicação independente para custos, compras, formação de preços e contas a pagar. A calculadora técnica existente não é acessada ou modificada. Dados persistem no navegador; exporte backups regularmente.

## Aviso sobre a base de testes

**Zerar a base remove os cadastros, compras, custos, parcelas, pagamentos, auditoria e orçamentos deste navegador. Exporte um backup antes de limpar.** A recuperação depende de restaurar esse arquivo em Configurações.

Os dados financeiros ficam no localStorage e não são enviados ao GitHub. Um commit ou uma atualização do site não limpa nem sincroniza dados entre máquinas. Após uma limpeza, recarregue as demais abas abertas do painel para evitar que uma aba antiga grave novamente o estado anterior.

## Instalação e execução

Requer Node.js 20+ e navegador moderno.

1. Execute `npm install` para instalar a dependência usada nos testes XML.
2. Execute `npm start` ou `node server.mjs`.
3. Acesse http://127.0.0.1:4173 . Não abra index.html diretamente.
4. Execute `npm test` para os testes de domínio, importação e telas com DOM simulado.
5. Para os testes do parser nativo do navegador, abra /xml-tests.html. A nota em /nfe-demonstracao.xml é fictícia, identificada como DEMONSTRAÇÃO.

A aplicação não requer dependências no navegador nem compilação. O pacote @xmldom/xmldom é somente de desenvolvimento. O acesso ao mesmo site em outro navegador/máquina começa com outra base. Para transportar dados: Exportar backup → Configurações → Restaurar backup. Limpar os dados do navegador remove a base local.

## Importação fiel da NF-e

Em **Entradas e compras**, selecione o XML autorizado da NF-e. O painel lê fornecedor, CNPJ/CPF, código com zeros à esquerda, descrição, unidade, quantidade, preço unitário original, descontos, frete, seguro, outras despesas e impostos. Os dados originais e o XML integral são preservados no registro local e no backup.

O fornecedor é identificado por CNPJ/CPF; novos materiais são identificados pelo par fornecedor + código do produto. O código do fornecedor é mantido, inclusive quando dois fornecedores usam o mesmo código. Para material já existente de outro fornecedor, o vínculo pode ser escolhido explicitamente. A descrição original da nota permanece preservada mesmo quando o cadastro interno tem outra descrição.

Antes de confirmar, escolha **Matéria-prima**, **Acessório** ou **Componente** para cada item cadastrado. Revise unidade de consumo e fator de conversão: barra de 6 metros para consumo em metros = fator 6. Uma unidade de compra alterada requer confirmação explícita da conversão. O último fator informado é reapresentado nas importações seguintes do material.

Quando frete, seguro, outras despesas ou descontos já estiverem distribuídos nos itens e sua soma conferir com os totais, a distribuição original é respeitada. Se não houver distribuição nos itens, o total destacado é rateado proporcionalmente ao valor líquido dos produtos, com distribuição dos centavos restantes por maiores frações. Distribuições parciais ou divergentes nos itens bloqueiam o registro para conferência. Valores originais por item continuam disponíveis para conferência.

O preço unitário original do XML é exibido separadamente do custo unitário com despesas. ICMS, PIS, COFINS e tributos aproximados são exibidos, sem nova soma ao preço. IPI, II, ICMS-ST, FCP-ST e IPI devolvido destacados como adicionais são incorporados à composição, conforme os totais da própria nota; desoneração dedutível é tratada separadamente. O total calculado precisa fechar em centavos com vNF e o total dos produtos também precisa conferir. Diferenças bloqueiam o registro para conferência; nenhum ajuste arbitrário é criado para forçar o fechamento. Esta conferência não substitui a validação fiscal ou a revisão contábil de créditos tributários.

Todas as duplicatas são lidas de cobr/dup com o valor e vencimento originais. A soma deve coincidir com o total da nota. XML sem duplicatas exige informar um vencimento confirmado; a data atual não é presumida. Condições de pagamento atípicas, como adiantamentos fora das duplicatas ou duplicatas que não somam vNF, exigem conferência antes do cadastro. As informações de pag/detPag são preservadas; não geram uma quitação bancária automática.

Ao confirmar a importação, fornecedor, novos materiais, compra, parcelas e propostas de custo são gravados em conjunto. Falhas de conferência não deixam cadastros parciais. A chave de acesso impede duplicidade. Desmarcar um item exclui apenas o cadastro/atualização do material: o registro da nota e suas parcelas continuam pelo valor integral.

## Aprovação pelo total da nota

Em **Aprovação de custos**, selecione a nota, informe o responsável e confirme a decisão. A tela mostra o total integral e os vencimentos antes da aprovação. Aprovar ou rejeitar decide todos os custos vinculados à nota em conjunto; não é permitido aprovar apenas um item de uma nota neste fluxo.

O custo dos materiais permanece pendente até a aprovação. Linhas do mesmo material na nota são agrupadas por média ponderada da quantidade de consumo. Auditoria registra decisões de custo e, separadamente, a decisão sobre o valor total da nota. O pagamento das parcelas não altera o custo aprovado. Notas antigas já importadas pela versão anterior mantêm seus registros históricos; o fluxo integral é aplicado às novas importações.

## Lançamento manual

O documento/referência é **opcional**. Informe fornecedor, situação documental, emissão, recebimento, material, conversão, valores, parcelamento e vencimento. Desconto, frete e outras despesas são campos separados. Valores de referência manual vazios são aceitos. Pagamentos parciais e totais ficam em **Contas a pagar**, com histórico por data de pagamento.

## Demais módulos

- **Materiais e fornecedores:** cadastro manual também disponível. Todo custo inicial exige aprovação. Cabeçotes e flanges são conjuntos completos com solda incluída.
- **Equipe e produção:** cadastre salário, provisões, encargos agregados incluindo FGTS e horas produtivas. Custo/hora = salário × (1 + provisões de 13º e férias) × (1 + encargos) / horas produtivas. A base dos encargos é salário e provisões. Etapas usam minutos por bloco iniciado de 100 mm por elemento, mesma referência entre diâmetros.
- **Custos e preços:** informe dimensões finais após compactação, elementos e consumos técnicos. Bases: conjunto, por elemento ou por metro de elemento. Nipples sugerem 2 por elemento, ajustáveis. Terminais exigem quantidade informada. Cabeçotes/flanges usam conjunto completo; evite duplicar solda já incluída.
- **Rateios e preço:** fabricação e administrativo são valores atribuídos em reais por orçamento. Informe somente despesas fora dos materiais, acessórios e mão de obra. Tributos de venda e margem são configurados pela RR. Preço por margem = custo / (1 − tributos − margem); preço por markup = custo × multiplicador. Margem bruta deduz materiais, acessórios, mão de obra e fabricação; margem líquida estimada deduz também administrativo e tributos. Descontos mostram o efeito no resultado. Orçamentos preservam uma cópia dos custos utilizados.
- **Contas a pagar:** registre quitações totais ou parciais sem exceder o saldo. Parcelas manuais são mensais, ajustadas ao último dia disponível e preservando centavos. Parcelas de XML seguem as datas exatas do documento.
- **Relatórios:** três últimos meses completos por padrão, início inclusivo e fim exclusivo. Entradas usam recebimento; compromissos usam vencimento; quitações usam data do pagamento. Comparação com período anterior de mesma duração em dias. Excel usa HTML tabular em .xls e pode mostrar aviso de formato; PDF usa Salvar como PDF na impressão.
- **Configurações:** backups JSON e exemplo demonstrativo em base vazia. Restauração substitui a base após confirmação.

## Organização

- dist/domain.mjs: cálculos, custos, parcelas e aprovações.
- dist/nfe.mjs: parser NF-e, conciliação, rateio, cadastro automático e decisão integral.
- dist/store.mjs: repositório local substituível por API/banco futuro.
- dist/app.mjs: interface e coordenação das operações.
- technical-input.schema.json: contrato futuro de dimensões e elementos; integração não executada.
- tests/domain.test.mjs, tests/nfe.test.mjs, tests/ui.test.mjs: testes automatizados com fixture fictícia.

## Validação e limites

29 testes automatizados passaram: domínio, XML com parser real, aprovação integral, prevenção de duplicidade, fechamento de centavos, parcelas e renderização com DOM simulado. Automação visual do navegador indisponível por timeout; testes do DOMParser nativo e layout precisam de validação manual.

Primeira versão de testes, sem autenticação de operadores, validação fiscal, banco compartilhado ou conciliação bancária. Auditoria local não é imutável. Backups têm validação estrutural básica: use arquivos confiáveis. Importações com novos grupos fiscais ou regras de composição não suportadas são bloqueadas quando não fecham com os totais, para evitar custo estimado incorreto. Não há integração com SEFAZ. Ainda pendentes: edição de cadastros/parcelas, rateios mensais automáticos, XLSX/PDF nativos e comparações por meses de calendário.
