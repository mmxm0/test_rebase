# test_rebase - Voltando no tempo com o git

Sabe quando você tá lá, de boas fazendo os seus commits no repositório e de repente se dá conta que fez alguma coisa que não devia fazer? *exempli gratia:* - commitou com o usuário da empresa no seu repositório pessoal...

Bom, pra isso, existe o Rebase, uma ferramenta poderosissíma dentro do universo git. 
O rebase no Git é um processo que permite reescrever o histórico de commits, movendo ou combinando commits de uma branch para outra, de maneira linear. Em vez de criar um merge commit, o rebase aplica os commits da branch atual em cima de outra branch, criando um histórico mais limpo e linear. Isso facilita a compreensão do histórico do projeto, eliminando bifurcações e merge commits desnecessários. Contudo, deve-se ter cuidado ao usar o rebase em branches compartilhadas, pois pode causar problemas de consistência no histórico para outros colaboradores.

Se você precisa usar, vem comigo que eu vou te ensinar.

## 1 Passo

Abra uma instância de terminal na raiz do seu repositório. 
(Existem 1001 uma formas de fazer isso, use a que mais lhe agradar)

## 2 Passo

Agora vem a parte mais legal. 
O rebase. Se a ~~merda~~ *commit* que você fez foi no último *push*, você pode usar o seguinte comando:

´´´
git rebase -i HEAD~1

´´´

onde 1 representa a quantidade de *commits* que você quer fazer o rebase. 
isso significa que o 1 commit anterior será considerado no rebase.
Se você quiser que sejam considerados mais commits é só alterar esse número, lembrando que é uma pilha. O 1 commit é o mais recente. se você quer alterar os dois ultimos commits, você coloca 2 ao invés de 1 e assim sucessivamente.

*Ai mas eu ~~fiz merda~~ dei push com coisas erradas em todos os commits.* 
- Não tem problema. Use o seguinte comando:

´´´
git rebase -i --root
´´´

*Ai mas eu ~~fiz merda~~ dei push com coisas erradas em um commit lá em 1999.* 

- Ainda não tem jeito pra ~~burrice crônica~~ falta de atenção. Mas pra esse problema aí, a gente também tem um comando no git que resolve. Você só vai ter mais um pouquinho de trabalho... Rebase interativo.

### 2.1 Passo

Você primeiro vai precisar identificar o hash do seu commit problemático que você deseja rebasear.. 
 Você pode listar os commits recentes com:
´´´
git log --oneline

´´´
Ou simplesmente ir lá na interface e procurar pelo hash.

é uma cadeia alfanumerica diferentona que fica do lado de cada commit, e se você não sabe o que é, deus te ajude nessa jornada. Pegue na mão do chatGPT pra isso.

Voltando, Inicie o rebase interativo:
´´´
git rebase -i <commit_hash>~1
´´´
e.g. o hash é *296124c*, então seu comando vai ser git rebase -i 296124c~1

## 3 Passo
O comando anterior abrirá um arquivo chamado ´´´git-rebase-todo´´´´ no seu editor oficial utilizado pelo git. 
Nesse arquivo a gente vai informar o que queremos fazer com os commits em questão. Nesse caso, vamos editar o commit pra alterar o autor.
Então ao invés de pick você vai mudar pra edit.
Feito isso, salve o arquivo. volte pra o terminal do git.
Lá vai aparecer a senguinte saída:

´´´
You can amend the commit now, with

  git commit --amend 

Once you are satisfied with your changes, run

  git rebase --continue

´´´

## 4 Passo
Jóia, agora é que vem ~~o cornojob~~ a parte mais repetitiva.
para cada um dos commites que vamos rebasear, iremos alterar o autor com o seguinte comando:

´´´
git commit --amend --author="<GIT_USER> <<GIT_EMAIL>>" --no-edit
´´´
*e.g. git commit --amend --author="mmxm0 <meuemail@meuinmail.com>" --no-edit


após isso, você vai continuar para o próximo commit que selecionou lá no passo 3. 
para isso, digite o comando abaixo:

´´´
git rebase --continue
´´´

esse processo ocorre pra cada commit selecionado. 
Durante ele você vai perceber que o indicador da branch que você tá trabalhando vai mudar *e.g. (main|REBASE)* isso acontece por que quando estamos fazendo um rebase, não estamos operando diretamente sobre a branch em questão, mas em uma versão do passado dela. Tá tudo bem. Se o Doutor Estranho conseguiu, você também consegue fazer isso. 

se não tiver mais commit pra você fazer rebase você vai receber a seguinte saída do prompt:

*Successfully rebased and updated refs/heads/main.*

## 5 Passo

agora vamos ~~fechar a tampa do caixão~~ subir as alterações feitas no rebase para o repositório remoto.
Nessa etapa é importante destacar que: não é possivel enviar de volta os commits modificados com apenas um simples  *"git push"*. Isso porque estamos reescrevendo a linha do tempo do repositório, isso implica substituir a branch atual que existe lá no remoto por uma outra, modificada no ato do seu rebase. então meu amigo, vai ter que ser na força. Digite o comando abaixo.

´´´
git push --force
´´´

# Conclusão
Você conseguiu! Voltou no tempo e com sorte aprendeu uma lição valiosa sobre atenção na hora de commitar.

Pra que não aconteça de novo, meu amor pode simplesmente aproveitar que tá com um terminal aberto no repositório em questão e digitar o seguinte comando:

´´´
git config --local user.email "<GIT_EMAIL>" --replace-all
git config --local user.name "<GIT_USER>" --replace-all
´´´
Pronto, não vai mais esquecer de subir as coisas com o nome errado.

Lembrando que: Isso só vale pro repositório em questão, os outros seguem com o autor global.

Então é isso, valeu pela paciência. E se tiver qualquer dúvida. Eu não estou aqui.

# Considetações finais E IMPORTANTES SOBRE O USO DO REBASE

Conforme mencionado no inicio desse artigo, É PRECISO TER MUITO MUIIIIIIIIIITO CUIDADO COM O USO DO REBASE EM BRANCHS QUE SÃO COMPARTILHADAS COM OUTRAS PESSOAS, *id est*, outras pessoas também usam e fazem commit nela.
Viagem no tempo tem consequências meu amigo, não pense que é só ir lá consertar as coisas, xablau tá tudo certo. Então para que você evite mais dor de cabeça pro seu lado.

Para evitar problemas de consistência no histórico ao usar o rebase, especialmente em ambientes colaborativos, siga estas práticas:

- Evite Rebase em Branches Compartilhadas: Nunca faça rebase em uma branch que já foi compartilhada com outros colaboradores. Isso altera o histórico, criando inconsistências para aqueles que já puxaram as mudanças.

- Use git pull --rebase: Ao integrar mudanças de uma branch remota, use git pull --rebase em vez de um simples git pull. Isso aplica seus commits locais no topo das mudanças recém-puxadas, evitando merge commits desnecessários.

- Comunicação: Se precisar fazer rebase em uma branch compartilhada, avise todos os colaboradores para que eles possam coordenar suas atividades e evitar conflitos.

- Forçar Push (com Cuidado): Se fizer rebase em uma branch que já foi enviada ao repositório remoto, você precisará usar git push --force (git push -f). Tenha cuidado, pois isso substitui a história remota, afetando outros que trabalharam nessa branch.

- Rebase Interativo: Use rebase interativo (git rebase -i) para reordenar, combinar ou editar commits, garantindo que você esteja consciente de cada mudança feita durante o rebase.

- Crie Branches Temporárias: Antes de rebasear, crie uma nova branch temporária a partir da branch atual para preservar o estado original. Se algo der errado, você pode voltar facilmente.

Seguindo essas práticas, você ~~ vaievitar barulho com os coleguinhas~~  minimizará o risco de causar problemas de consistência no histórico ao colaborar com outros desenvolvedores.