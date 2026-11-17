# Painel Financeiro da Elvira — primeira versão de testes

Aplicação independente em português para RR Resistências Industriais. Não conecta nem modifica a calculadora técnica. Base inicial vazia; o único exemplo disponível exige ação explícita e recebe prefixo DEMONSTRAÇÃO.

## Instalação e execução

Requer Node.js 20 ou superior e navegador moderno. Não há dependências de execução nem etapa de compilação.

1. Abra um terminal nesta pasta.
2. Execute `node server.mjs` (ou `npm start`).
3. Acesse http://127.0.0.1:4173 . Não abra index.html diretamente.
4. Execute `node --test tests/domain.test.mjs` (ou `npm test`).
5. Testes de XML com DOMParser nativo: abra http://127.0.0.1:4173/xml-tests.html . Esses quatro testes foram implementados; não foram executados nesta entrega devido à indisponibilidade da automação do navegador.

Mantenha o mesmo endereço e navegador para acessar a mesma base. Os dados ficam no localStorage, não na pasta do servidor. Alterar porta, host ou perfil cria outra base. Limpar o navegador remove os dados. Exporte backups JSON com frequência.

## Como usar cada módulo

1. **Materiais e fornecedores:** cadastre o fornecedor antes do material. Informe unidade de compra e consumo. O custo inicial é uma proposta, ainda indisponível para orçamento. Cabeçotes e flanges são acessórios pelo conjunto completo, já com solda incluída.
2. **Entradas e compras:** registre recebimentos manuais com situação documental, emissão e recebimento separados. Na importação XML, selecione os itens, vincule aos materiais existentes e revise a conversão. Exemplo: barra de 6 metros, consumo em metros, fator 6. Custos efetivos = (produtos − descontos + frete + outras despesas atribuídas) / quantidade de consumo. Tributos destacados não são adicionados automaticamente. Revise tributos recuperáveis e custos adicionais com a contabilidade; eventuais valores atribuídos entram em outras despesas.
3. **Aprovação de custos:** selecione propostas, informe o responsável e confirme aprovação ou rejeição. A aprovação atualiza o custo usado em novos orçamentos. A rejeição preserva o custo. O lote permite uma proposta por material para evitar resultado dependente de ordem. Auditoria registra custo vigente, custo proposto, responsável e data. Nome informado não equivale a autenticação.
4. **Equipe e produção:** informe salário, provisão de 13º, provisão de férias e adicional, encargos agregados incluindo FGTS, horas efetivamente produtivas. Custo/hora = salário × (1 + provisões) × (1 + encargos) / horas. A base dos encargos é salário mais provisões nesta versão. Cadastre etapas e minutos por bloco iniciado de 100 mm por elemento. Essa interpretação usa arredondamento para cima e deve ser validada pela RR. A referência não varia pelo diâmetro.
5. **Custos e preços:** informe dimensões finais, elementos e consumos técnicos. A base por metro multiplica comprimento/1000 e elementos; por elemento multiplica elementos; conjunto mantém quantidade fixa. Nipples sugerem duas unidades por elemento; terminais requerem quantidade informada. Cabeçotes e flanges usam conjunto completo. Não crie outra linha de solda quando já incluída. Cadastre etapas produtivas antes de calcular.
6. **Rateios e preço:** os valores atribuídos de despesas de fabricação e administrativas são informados em reais por orçamento; não há rateio automático. Inclua apenas despesas fora de materiais, acessórios e mão de obra. Tributos de venda e margem são parâmetros obrigatórios, sem alíquota presumida do Simples. Método margem: preço = custo total / (1 − tributos − margem desejada). Método markup: preço = custo × multiplicador. Desconto incide sobre o preço. Margem bruta = (venda − materiais − acessórios − mão de obra − fabricação) / venda. Margem líquida estimada deduz também administrativo e tributos. Salvar orçamento preserva uma cópia dos custos da ocasião.
7. **Contas a pagar:** parcelas mensais preservam centavos e ajustam vencimentos ao último dia disponível do mês. Registre pagamentos parciais ou totais com data e valor. O saldo determina a situação; uma parcela parcialmente paga pode continuar em atraso, destacado pelo saldo vencido no painel. Pagamentos não alteram o custo dos materiais.
8. **Relatórios:** padrão dos três últimos meses completos; início inclusivo e fim exclusivo. Entradas usam recebimento; compromissos usam vencimento; quitações usam data do pagamento. Compare com período imediatamente anterior de igual duração em dias. Exportação Excel é HTML tabular com extensão .xls, podendo exibir aviso de formato no Excel; não é arquivo .xlsx. PDF usa impressão do navegador: escolha Salvar como PDF.
9. **Configurações:** exporte e restaure backups JSON. A restauração substitui toda a base após confirmação. Carregue demonstração somente em base vazia.

## Organização e integração

- dist/domain.mjs: cálculos, aprovações, parcelas, registro de compras e extração NF-e.
- dist/store.mjs: repositório local; substituir por uma implementação com API/banco em etapa futura.
- dist/app.mjs: interface e coordenação de operações.
- dist/style.css: apresentação responsiva e impressão.
- technical-input.schema.json: contrato de dimensões/elementos para futura integração; nenhum acesso GitHub ou integração é executado.
- tests/domain.test.mjs: dez testes automatizados de regras de negócio.
- dist/xml-tests.html: quatro testes XML para navegador.

## Limites e próxima etapa

Esta é uma versão inicial local para validação, não um sistema contábil ou multiusuário. Não há autenticação, conciliação bancária ou validação fiscal da NF-e. A auditoria local pode ser alterada com acesso ao navegador. Backup tem validação estrutural básica; importe somente arquivos confiáveis produzidos por esta aplicação.

A importação lê frete/desconto/despesas por item; não reconcilia diferenças entre totais globais da nota e seus itens. Notas com totais sem distribuição nos itens precisam de lançamento manual com atribuição revisada. O valor financeiro importado corresponde somente aos itens selecionados, não à nota inteira. Parcelamento é mensal e a interface ainda não permite editar cada vencimento individualmente. Alterações cadastrais e correções de lançamentos exigem próxima etapa; preserve backups antes dos testes. Não há controle automático de dupla contagem: a separação dos grupos torna a revisão explícita.

O painel resume próximos compromissos, mas a ordenação de datas e o indicador de evolução ainda devem ser refinados; evolução detalhada está na tabela do relatório. A comparação anterior usa igual duração em dias, não três meses de calendário anteriores. Ainda faltam: exportação nativa XLSX/PDF, conciliação de XML por totais, edição de parcelas e cadastros, rateios mensais, testes visuais e validação operacional pela RR.

## Validação realizada

Dez testes de domínio aprovados. Sintaxe de app.mjs conferida. Nove telas renderizadas com DOM simulado sem exceções; servidor local respondeu HTTP 200. Automação visual do navegador indisponível (timeout), portanto layout e fluxos completos ainda precisam de validação manual. Os testes de XML no navegador não foram executados.
