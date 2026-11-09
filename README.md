# Sumaúma

**Painel de controladoria para uma carteira de processos periciais.**

*Sumaúma* é a maior árvore da Amazônia: suas raízes sustentam a floresta ao redor. O projeto tem a mesma função: dar sustentação à gestão, reunindo num só lugar o que antes ficava espalhado em tarefas, etiquetas e planilhas.

> Vitrine do projeto. O código, a base de dados e os relatórios são privados. Esta página descreve o problema, a arquitetura e as decisões de projeto, sem expor dados, nomes, instituições, processos ou credenciais.

---

## O problema

Uma operação pericial acompanha centenas de processos ao mesmo tempo, cada um com várias tarefas abertas em um sistema de gestão jurídica. O acompanhamento era feito tarefa a tarefa:

- não havia uma visão da carteira inteira;
- pendências atrasadas eram descobertas tarde;
- relatórios gerenciais eram montados à mão, a cada pedido;
- controladoria, jurídico e gerência olhavam números diferentes para a mesma pergunta.

A pergunta que o projeto responde: **onde cada processo parou e o que precisa acontecer para ele avançar.**

## A solução

Uma rotina diária, sem intervenção humana, que coleta a carteira, confere a consistência dos dados e gera um painel único para três públicos:

| Público | O que consulta |
|---|---|
| Controladoria | Pendências, responsáveis e continuidade dos processos |
| Jurídico | Tarefas, observações e próximas providências |
| Gerência | Carteira, entregas, produtividade, honorários e comparativos |

## Como funciona

```
Agendador (todo dia, de manhã)
        │
        ▼
Coleta somente leitura no sistema de gestão
(exportações de processos, tarefas abertas e histórico de conclusões)
        │
        ▼
Conferência de consistência ──── divergência? ──► painel anterior é mantido
(contagens, identificadores, períodos, correspondência entre exportações)
        │ ok
        ▼
Histórico local (SQLite) + cálculo dos indicadores
        │
        ▼
Painel (Excel) publicado em pasta sincronizada
```

## O que o painel mostra

- **Carteira e prioridades:** todos os processos, ativos e encerrados, com uma fila que destaca uma tarefa por processo: vencidas, do dia, futuras e processos sem tarefa aberta.
- **Entregas e produtividade:** primeira entrega de cada processo, separada de revisões e complementos, e tarefas concluídas por mês e por executor.
- **Honorários:** valor proposto separado do valor aceito, recorte de alta complexidade por limiar configurável e valores ausentes destacados para conferência.
- **Comparativos:** volume, honorários e tempos por órgão julgador, sempre com o tamanho da amostra informado.

## Decisões de projeto

1. **Somente leitura.** A rotina nunca altera cadastros, tarefas ou lançamentos na fonte.
2. **Divergência bloqueia.** Se as exportações não batem entre si, o painel anterior é mantido em vez de publicar um número errado.
3. **Dado ausente não vira zero.** Valor em branco aparece como "conferir", não como receita zero.
4. **Prioridade aponta, não decide.** A fila indica o que olhar primeiro; não é decisão jurídica nem confirmação de prazo.
5. **Cada indicador declara seu limite.** Data de cadastro não é início da perícia, conclusão de tarefa de pagamento não é crédito em conta, e quantidade de tarefas não mede esforço.
6. **Histórico preservado.** Cada coleta válida é guardada; nada é sobrescrito sem registro.

## Tecnologias

Python · SQLite · automação de navegador para exportações · Excel (openpyxl) · Agendador de Tarefas do Windows · HTML para a interface web (em prévia)

## Situação

| Parte | Situação |
|---|---|
| Coleta diária, conferência e histórico | Em produção |
| Painel Excel | Em produção, em validação com a equipe |
| Interface web com busca e detalhe por processo | Prévia; falta hospedagem com acesso restrito |
| Boletim matinal por e-mail | Pronto em prévia; desligado por decisão de projeto |
| Modelo de linguagem sugerindo o próximo passo | Piloto; não faz parte da rotina diária |

## Próximos passos

- Publicar a interface web com login restrito, inclusive no celular.
- Conciliar recebimentos antes de comparar prazos de pagamento.
- Retomar o piloto de IA com saída menor, citando a evidência usada e o que falta conferir.

---

Desenvolvido por [Elias Alencar](https://github.com/alesncar).
