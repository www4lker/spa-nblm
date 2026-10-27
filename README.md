# Oficina NLM

Páginas que montam prompts para o [NotebookLM](https://notebooklm.google.com),
pensadas para leitura acadêmica de fontes: livros, artigos, transcrições.

Site: https://www4lker.github.io/spa-nblm/

## O que é o NotebookLM, e por que ele pede prompts próprios

O NotebookLM é um assistente do Google, baseado no Gemini, que responde a
partir das fontes que você carrega num caderno (notebook). Cada resposta traz
números entre colchetes, `[1]`, `[2]`, que levam ao trecho citado. Isso o torna
útil para pesquisa, mas não o livra de inventar: ele pode atribuir a uma fonte
o que ela não diz, juntar duas fontes numa conclusão que nenhuma delas tira, ou
preencher o que falta com conhecimento geral.

O NotebookLM tem três lugares onde se escreve um prompt:

| Campo | Para quê | Limite |
|---|---|---|
| Instruções personalizadas do chat (*Custom Instructions*) | regra fixa, vale para toda conversa do caderno | 10.000 caracteres |
| Chat | a pergunta de cada vez | 4.000 caracteres |
| Painel Studio (relatórios, áudio, guias) | o pedido de um artefato | 5.000 caracteres |

O ponto que organiza a Oficina: **as instruções personalizadas do chat não
são herdadas pelo Studio.** Uma regra escrita só lá vale para as respostas do
chat, mas não entra sozinha num relatório ou num áudio gerado no Studio.

Há dois caminhos para levar um pedido ao Studio:

- **Direto:** colar o prompt no campo de personalização do próprio artefato.
- **Pelo chat:** pedir no chat que gere um artefato no Studio seguindo tal ou
  qual orientação. O chat escreve o comando e o entrega ao Studio. Funciona,
  mas quem redige o comando final é o modelo, não você: as regras só chegam
  se ele as copiar para dentro desse comando.

Por isso cada prompt destinado ao Studio leva consigo as próprias regras, e
cada página mostra para onde vai o prompt que gera e quanto do limite ele
ocupa.

### Conferir o que o Studio recebeu

Todo artefato do Studio guarda o pedido que o gerou. No menu de três pontos
ao lado do artefato, a opção **"Ver comando e fontes"** mostra o texto exato
do comando usado e a lista de fontes consultadas.

Parece detalhe, mas é o que permite trabalhar com controle:

- quando o pedido passou pelo chat, é ali que se vê o que o chat de fato
  escreveu, e se as regras foram junto ou ficaram pelo caminho;
- a lista de fontes mostra se o artefato usou todas as fontes do caderno ou
  só as que estavam marcadas no momento;
- um comando que deu bom resultado pode ser copiado dali e reaproveitado.

## As páginas

1. **Suite de Corpus.** Gera as instruções fixas do caderno (o *system
   prompt*), um diagnóstico das fontes carregadas e prompts de refinamento.
   Comece por ela.
2. **Dossiê de Confronto.** Põe fontes em confronto: auditoria, advogado do
   diabo, debate, matriz comparativa. Destino: chat.
3. **Laboratório.** Análises mais longas: pressupostos, lacunas, dialética,
   síntese entre fontes, roteiro de podcast. Destino: chat, ou Studio quando
   a saída é áudio.
4. **Prompt Studio.** Formatos do painel Studio (documento de briefing, guia
   de estudo, ensaio) com as regras embutidas.

Para usar, abra a página, preencha os campos, copie o prompt e cole no campo
indicado do NotebookLM. As páginas não enviam nada a lugar nenhum: são HTML
estático, sem servidor, e o texto digitado fica guardado só no seu navegador
(`localStorage`). O botão "Limpar campos" o apaga.

## As regras que os prompts carregam

As regras (*guardrails*) partem de dois trabalhos sobre alucinação:

- **Roig (2026)** mediu, em 35 modelos e 172 bilhões de tokens, quanto os
  modelos inventam ao responder com base em documentos fornecidos. Mesmo os
  melhores inventam a uma taxa que não é desprezível, e a taxa cresce com o
  tamanho do contexto. O achado que mais pesa aqui: **achar a informação na
  fonte e resistir a inventar são capacidades distintas.** Um modelo que
  localiza bem os fatos ainda pode fabricar fatos que não existem. Ancorar a
  resposta nas fontes (*grounding*) não basta.
- **Kalai et al. (2025)** argumentam que os modelos alucinam porque o
  treinamento e as avaliações premiam o palpite e punem a admissão de
  incerteza. O modelo aprende que chutar rende mais do que dizer "não sei".

Daí as regras, que tentam tornar a resposta honesta mais barata que o palpite:

- **Citar** cada afirmação com a etiqueta da fonte e o número `[n]`, na mesma
  frase.
- **Marcar inferências.** Toda conexão que o modelo constrói entre fontes é
  declarada como dele, não atribuída a elas.
- **Preservar divergências.** Quando as fontes discordam, o conflito é o
  achado, não um problema a resolver.
- **Aceitar a lacuna.** "As fontes não abordam este ponto" é resposta válida.
- **Não fabricar dados bibliográficos.** Página, editora, ano ou DOI ausente
  da fonte fica marcado como ausente.
- **Fidelidade.** Reconstruir o argumento de cada fonte com a força que o autor
  lhe deu, sem acrescentar ressalva ou "outro lado" que a fonte não traga.
  Crítica, quando pedida, vem depois e marcada como do modelo.
- **Estilo.** Frases diretas, sem dramatização. Analogia e metáfora só quando
  estão na própria fonte.

Nada disso garante que o modelo obedeça. As regras reduzem a margem para
inventar e tornam a invenção mais fácil de ver; a conferência continua sendo
de quem lê.

## Por que tags XML

Os prompts são escritos em blocos marcados, como `<citacao>…</citacao>` ou
`<fidelidade>…</fidelidade>`, em vez de texto corrido. Três motivos:

- **Separar instrução de material.** O texto que você cola (um foco, um nome de
  fonte, um resumo) fica dentro de uma tag própria, e o modelo não o confunde
  com uma ordem.
- **Montar por peças.** Cada regra é um bloco que entra ou sai conforme o
  destino. O prompt do Studio leva `<guardrails>` e `<estilo>`; o do chat não
  precisa, porque as instruções fixas já os trazem. Sem blocos, isso seria
  costura de parágrafos.
- **Economia.** Na conversão do system prompt para XML, a prosa cortada rendeu
  mais do que as tags custaram, num campo onde cada caractere conta.

Estruturar prompts com tags XML é prática recomendada na documentação da
Anthropic para o Claude; não encontramos recomendação equivalente na
documentação oficial do Gemini. Não temos
medição que mostre que o NotebookLM obedece melhor a um prompt em XML do que ao
mesmo prompt em prosa: a escolha se apoia nos três motivos acima. Os nomes das
tags estão em português, como o resto do texto.

## Referências

- KALAI, Adam Tauman; NACHUM, Ofir; VEMPALA, Santosh S.; ZHANG, Edwin. *Why
  Language Models Hallucinate*. arXiv:2509.04664, 2025.
  https://arxiv.org/abs/2509.04664
- ROIG, JV. *How Much Do LLMs Hallucinate in Document Q&A Scenarios? A
  172-Billion-Token Study Across Temperatures, Context Lengths, and Hardware
  Platforms*. Kamiwaza AI, mar. 2026.
