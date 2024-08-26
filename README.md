# Bem-vindo à prática de Git do The Missing Semester!
Este repositório é um lugar seguro para praticar os conceitos básicos do git. Tente fazer um rebase sem arriscar nenhum código importante! Aprenda como sincronizar a partir de um repositório upstream! Pratique a resolução de conflitos de merge!
Este guia pressupõe que você tenha um conhecimento confortável de comandos do shell unix. Se você quiser uma atualização, [tente aqui](https://github.com/you-dont-need/You-Dont-Need-GUI). Este guia também pressupõe que você tenha uma conta existente do github e tenha [adicionado sua chave ssh a ela](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/).

Este guia foi projetado como um espaço para praticar comandos git, não apenas para ensiná-los. Se você está procurando apenas uma referência rápida, recomendo http://rogerdudler.github.io/git-guide/

## O que são git e GitHub?
Git é um sistema de controle de versão. Ele rastreia alterações em arquivos e permite que as pessoas colaborem em projetos. O Git é frequentemente usado para projetos de software, mas pode ser usado para qualquer projeto e é especialmente poderoso para projetos baseados em arquivos de texto. Um projeto no git é chamado de "repositório" (ou repo, para abreviar). Um repo é um monte de arquivos e diretórios. Cada repo inclui um diretório .git que contém informações sobre todas as alterações feitas no repositório.

O GitHub é um serviço de hospedagem online para git. Embora a maior parte do seu trabalho com o git ocorra no seu próprio computador, é frequentemente útil sincronizar seu repositório local com um repositório "remoto" (no GitHub), permitindo que você trabalhe em vários computadores ou colabore com outras pessoas em um projeto.

Existem muitas alternativas ao GitHub, como Stash, GitLab ou auto-hospedagem de seus repositórios remotos.

## Init
Para criar um novo repositório, crie um diretório e use `git init` dentro dele.
```bash
mkdir repoTeste
cd repoTeste
git init
```
É isso, bem simples. Você deve ver um diretório `.git` lá agora.
```bash
ls -a # os diretórios que começam com . são normalmente ocultos, então use -a para mostrar todos
```
Não usaremos este novo repositório, então você pode excluí-lo.
```bash
rm -rf repoTeste
```

## Fork
Faça um fork deste repositório na sua conta do github clicando no botão Fork no github (no canto superior direito da página do github para este repositório). Isso criará um "fork" (cópia) deste repositório na sua conta do github, criando um novo repositório remoto que você controla. "Forking" é útil quando você deseja usar um repositório existente como base para seu próprio projeto. Um repositório "forked" também pode ser usado para propor alterações ao repositório original.

Se você não estiver usando o github para gerenciar seu repositório remoto, crie sua própria cópia remota deste repositório no serviço de hospedagem remoto que estiver usando.

## Clone
`clone` cria uma cópia local de um repositório remoto. Vamos clonar seu repositório "forked" da etapa anterior. Usaremos `ssh` ao clonar para que possamos interagir com o remoto sem ter que se preocupar com credenciais.
```bash
git clone git@github.com:username/MissingSemester-git.git # use seu nome de usuário aqui
```
Se você estiver clonando um repositório que não é de sua propriedade, precisará clonar usando https em vez de ssh. Você também não poderá enviar alterações para um repositório que não é de sua propriedade.

## Add e Commit
Agora que temos uma cópia local do repositório, vamos fazer uma alteração nele.
```bash
echo "It is a period of civil war" > arquivo.txt
git status
```
O comando `git status` mostra que temos uma alteração não confirmada. Para registrar uma alteração no git, primeiro devemos confirmar essa alteração no índice e, em seguida, confirmar todas as alterações confirmadas.
```bash
git add arquivo.txt # adicionando a alteração
git status # mostra que nossa alteração está confirmada e pronta para ser confirmada
git commit -m "Started telling a story of long ago & far, far away" # confirmar as alterações confirmadas com uma mensagem
```
A etapa de confirmação parece desnecessária neste caso, mas é útil em projetos complexos em que você está alterando vários arquivos. Vamos tentar um exemplo
```bash
echo "Rebel spaceships, striking from a hidden base, have won their first victory against the evil Galactic Empire." >> arquivo.txt # modificando nosso primeiro arquivo
echo "I love potatoes" > novoArquivo.txt # criando um novo arquivo
git status
```
Agora temos duas alterações e queremos confirmá-las separadamente, o que podemos fazer usando o comando de confirmação. Tente executar `git status` após cada comando abaixo para ver as alterações que você está fazendo.
```bash
git add arquivo.txt
git commit -m "Started the crawl"
git add novoArquivo.txt
git commit -m "Started my food blog"
```
Confirmações separadas são úteis ao confirmar alterações que são logicamente ou funcionalmente não relacionadas.

## `HEAD` e a git tree
No git, o `HEAD` é o ponteiro para a referência do branch atual. Mover a cabeça é como avançamos para confirmações mais recentes/antigas ou mudamos de branch. Você pode pensar na cabeça como um marcador "você está aqui". Vamos dar uma olhada em onde estamos
```bash
git log
```
Você pode ver `HEAD` no topo, mas esta não é uma visualização particularmente clara da árvore git. Se você tiver um programa git gráfico, poderá usá-lo para examinar a árvore git, mas prefiro ficar na linha de comando.
```bash
git log --graph --decorate --pretty=oneline --abbrev-commit --all # exibe um log muito mais bonito
```
Isso é muito melhor! Podemos ver `HEAD` claramente no topo, apontando para o branch `master`. Outros branches também são visíveis, mas ignore-os por enquanto.

Vamos salvar esse comando para usarmos mais tarde. Esta etapa é opcional, mas recomendada. Crédito para [Conrad parker](http://blog.kfish.org/2010/04/git-lola.html) para o comando lola.
```bash
git config --global alias.lola 'log --graph --decorate --pretty=oneline --abbrev-commit --all'
git lola
```

## Branches
Como você viu em `git lola`, nossa árvore git tem vários branches. Vamos listá-los todos:
```bash
git branch -l # mostrar todos os branches locais (deve ser apenas o master)
git branch -r # mostrar todos os branches remotos
git branch -a # mostrar todos os branches
```
Cada branch é um ponteiro para um instantâneo do seu projeto. Se você estiver trabalhando em vários recursos diferentes, sempre use um novo branch para cada um.

### Novo branch
Vamos criar um novo branch
```bash
git branch meuPrimeiroBranch # criar um novo branch
git lola
```
O que aconteceu? Não muito. Criamos um novo branch (instantâneo do nosso código), mas é isso. `HEAD` ainda aponta para o branch `master`, então qualquer alteração no código que fizermos será feita no `master`, não no nosso novo branch. Para mover `HEAD` para um branch diferente, use o comando `checkout`.
```bash
git checkout meuPrimeiroBranch
git lola
```
Agora podemos ver `HEAD` apontando para nosso novo branch. Muitas vezes, você desejará ir para um branch imediatamente após criá-lo. Você pode fazer isso em um único comando:
```bash
git checkout -b "blogCulinario" #crie um novo branch e alterne para ele imediatamente
git status
```

Se confirmarmos alterações no nosso novo branch, veremos que ele se tornará diferente do nosso branch master:
```bash
echo "You have failed me for the last time" >> arquivo.txt
git add arquivo.txt
git commit -m "Added Vader quote"
git lola
git diff master # compare com o branch master
```

## reset
### Redefinindo para um commit
Agora, vamos tentar alterar a árvore com `reset`. Este comando move o branch para o qual `HEAD` aponta (e move `HEAD` junto com ele). O comando é chamado de "reset" porque "reinicia" ou desfaz alterações.

O comando `reset` tem vários modos, então vamos começar com `--soft`. Primeiro, vamos criar um commit indesejado.
```bash
git checkout master
echo "please clean this up asdfasdfasd" >> mess.txt
git add mess.txt
git commit -m "added mess"
git lola
```
Agora redefiniremos para o pai do commit atual usando o atalho `HEAD~`. Você também pode mover para um commit específico especificando o hash do commit (visível em `git log` ou `git lola`) em vez de `HEAD~`.
```bash
git reset --soft HEAD~
git lola
```
Você pode ver que o branch master voltou para o commit anterior e `HEAD` se moveu junto com ele. Nenhum dos arquivos foi alterado, no entanto, e se executarmos `git status`, veremos que mess.txt está "comitadas" pronto para ser "comitado". `git reset --soft HEAD~` basicamente desfez nosso último commit.

Em seguida, o comportamento padrão para redefinir: `--mixed`. Isso desfará os commits (como `--soft`) e também desfará as alterações "comitadas", mas não alterará os arquivos. Execute `git lola` e `git status` após cada comando abaixo para ver as alterações que você está fazendo.
```bash
git commit -m "make a mess again"
git reset HEAD~
```
Depois de executar o padrão (`--mixed`) `reset`, você pode ver que `mess.txt` está presente, mas não "comitado". Desfizemos o commit e desfizemos a confirmação do arquivo.

Finalmente, a opção mais perigosa: `reset --hard`. Este comando desfará os commits, desfará as alterações confirmadas e removerá essas alterações de todos os arquivos. Suas alterações serão obliteradas para sempre\* então use com cuidado!
\*veja Reflog
```bash
git add . # confirme tudo o que acabamos de desfazer
git commit -m "make a mess AGAIN"
git reset --hard HEAD~
```
Se você olhar para o seu diretório de trabalho, poderá ver que `mess.txt` não está em lugar nenhum e `git status` está limpo.

Em resumo:
`reset --soft`: Mova o branch para o qual `HEAD` aponta (desfaça os commits)
`reset`: (`--mixed`) Mova o branch `HEAD` e faça o índice de confirmação parecer com o novo local HEAD (desfaça os commits e desfazer as alterações confirmadas)
`reset --hard`: Mova o branch `HEAD`, faça o índice de confirmação parecer com o HEAD e faça o diretório de trabalho parecer com o índice (desfaça os commits, desfazer as alterações confirmadas e remova todas as alterações. Use com cuidado).

No código acima, usamos `reset` para mover para commits no mesmo branch, mas não há nada que o impeça de redefinir para commits em branches diferentes.

#### Juntar commits
O reset é útil como um botão de desfazer, mas vamos tentar um exemplo mais interessante. Imagine que somos um desenvolvedor ocupado e fizemos um monte de mensagens de commit em nossa máquina local.
```bash
git checkout -b butternut
touch pumpkin.txt
git add pumpkin.txt
git commit -m "vegi"
echo "dear diary" >> pumpkin.txt
git add pumpkin.txt
git commit -m "yep"
echo "today I ate a radish" >> pumpkin.txt
git add pumpkin.txt
git commit -m "oops"
echo "I mean, I ate a pumpkin. What a day" >> pumpkin.txt
git add pumpkin.txt
git commit -m "max"
git lola
```
Essas mensagens de commit foram úteis para acompanhar nosso próprio progresso, mas o restante da equipe de desenvolvimento preferiria ver um ou dois commits que descrevem completamente cada alteração. Vamos substituir nossos commits incrementais por uma única mensagem de commit:
```bash
git reset HEAD~4
git add pumpkin.txt
git commit -m "Started a log of my food habits"
```
Agora retorne ao branch master para o restante do nosso trabalho
```bash
git checkout master
```

### Redefinindo um arquivo
`reset` também pode ser usado em um arquivo específico. Se você fizer isso, `HEAD` não se moverá, mas todas as outras ações de `reset` serão executadas. Vamos tentar. Execute `git status` conforme você for para ver as alterações.
```bash
git checkout master
echo "The empire did nothing wrong" > protest.txt # fazer uma mudança
git add protest.txt # confirmar o arquivo
git reset protest.txt # desfazer a confirmação do arquivo
git status
rm protest.txt # limpar nosso exemplo
```
Não há opção `--hard` ao redefinir um arquivo. Use o comando `checkout` em vez disso.

## checkout
### fazendo check-out de um commit
Como `reset`, o comando `checkout` manipula a árvore git e move `HEAD` para um commit específico. `git checkout [commit]` é semelhante a `git reset --hard [commit]`, mas `checkout` move apenas `HEAD`, não o branch para o qual `HEAD` aponta. Gosto de pensar no `checkout` como uma maneira de me locomover na árvore, enquanto `reset` é uma maneira de alterá-la. Vamos tentar.

```bash
git checkout HEAD~
git lola
```
Depois do checkout, agora estamos em um estado de "cabeça destacada" - não estamos mais trabalhando em um branch. Ao contrário do que aconteceu quando fizemos `git reset`, ainda podemos ver todos os nossos commits anteriores na árvore git e podemos retornar facilmente ao nosso branch anterior
```bash
git checkout master
git lola
```

`checkout` também tentará mesclar quaisquer alterações não confirmadas e pararemos se elas forem substituídas ao fazer check-out do novo local.
```bash
echo "stop" > arquivo.txt
git status
git checkout HEAD~5
```
o git não nos deixará fazer check-out porque temos alterações não confirmadas

### fazendo check-out de um arquivo
Você também pode usar `checkout` em um caminho de arquivo. Isso não moverá `HEAD`, mas desfará as alterações confirmadas no arquivo no índice e também substituirá esse arquivo. `git checkout [branch] file` faz o que você esperaria de `git reset --hard [branch] file`, exceto que o último comando não existe.

Vamos usar o checkout para remover nossas alterações indesejadas para rastrear
```bash
git checkout master arquivo.txt
```

Reset e checkout são ferramentas poderosas e, com grande poder, vem grande complexidade. Recomendo [ler mais](https://git-scm.com/book/en/v2/Git-Tools-Reset-Demystified) sobre esses dois comandos.

## Stash e pop
Às vezes, você deseja armazenar temporariamente algum trabalho, mas não confirmá-lo (por exemplo, se quiser verificar rapidamente outro branch ou se perceber que começou a trabalhar no branch errado. `git stash` permitirá que você faça isso. `git stash pop` recuperará as alterações estocadas mais recentemente (as alterações estocadas são armazenadas usando uma pilha).
```bash
git checkout master
echo "temporary mcTempFace" >> temp.txt
git add temp.txt
git stash # armazene temporariamente nosso trabalho porque este é o branch errado
git checkout blizzard # mude para o branch correto
git stash pop # recupere o trabalho armazenado
git add .
git commit -m "added something temporary"
```
Nunca use stash para armazenamento de longo prazo, porque garanto que você esquecerá o que armazenou e em qual branch ele deveria estar. Se você deseja armazenar algo por mais de um dia e não deseja que ele esteja em nenhum de seus branches, basta colocá-lo em um novo branch no local relevante.

## Mesclando
Quando você deseja incorporar código de um branch em outro branch, é hora de fazer um `merge`.

### Merge resolvido automaticamente
A maioria das mesclagens é simples e o git as manipula completamente automaticamente
```bash
git checkout merge-target
git merge simple-feature # mescla o recurso simples no branch atual (merge-target)
git lola
```

### Conflitos de merge

#### Resolução manual
Às vezes, uma operação de merging exigirá intervenção manual:

```bash
git checkout merge-target
git merge complex-feature
git status
```

Ops, a operação de merging encontrou alguns conflitos e o Git não tem certeza de como resolvê-los. O comando `git status` nos informa que esses conflitos estão no arquivo `crawl.txt`. Abra esse arquivo em seu editor de texto favorito e você verá marcadores de conflito no texto, semelhantes a isto:

```
<<<<<<< HEAD
It is a planet of civil war.
=======
It is a period of PARTIES and maybe war.
>>>>>>> complex-feature
```

O texto entre `<<<<<<< HEAD` e `=======` é do branch `merge-target` (seu branch atual), enquanto o texto entre `=======` e `>>>>>>> complex-feature` é do branch `complex-feature`. Para resolver o conflito, edite o texto conforme desejar e, em seguida, exclua os marcadores de conflito. Quando terminar, salve seu trabalho e volte para o terminal.

```bash
git add crawl.txt
git merge --continue
```

Você também pode usar uma ferramenta de merge (como kdiff, meld ou beyond compare) para resolver mesclagens. Embora eu prefira usar a linha de comando para a maior parte do meu trabalho com o Git, uma ferramenta de mesclagem gráfica é indispensável quando se enfrenta mesclagens complexas.

#### "Ours" vs "Theirs"

Às vezes, quando você tem conflitos de merge, você só quer aceitar todas as alterações de um branch específico para cada conflito.

```bash
git checkout merge-target
git merge complex-feature-2
```

Ao realizar o merge, "ours" significa o branch no qual você está mesclando, enquanto "theirs" significa o branch do qual você está mesclando. Neste caso, estamos mesclando no branch `merge-target` e mesclando a partir do branch `complex-feature-2`. Decidimos que queremos apenas as alterações do branch `complex-feature-2`, ou "theirs", então podemos evitar fazer uma mesclagem manual completa (hurra).

```bash
git checkout --theirs funky.txt # aceite todas as alterações do branch de recurso
git add funky.txt
git merge --continue
```
