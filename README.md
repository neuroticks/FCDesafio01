<h1>FCDesafio01</h1>

<h2> Warnings: </h2>
<ul>
    <li> 
        <p style="text-align: justify; font-size: 1.2em;">
        Este não é um tutorial de Docker ou afins, nem de Golang!
        </p>
    </li>
    <li> 
        <p style="text-align: justify; font-size: 1.2em;">
        É a realização de uma tarefa de final de módulo sobre Docker, do curso Full Cycle.
        </p>
    </li>
    <li> 
        <p style="text-align: justify; font-size: 1.2em;">
        Mas nada impede que algo de bom você consiga extrair se souber o que procura.
        </p>
    </li>
</ul>

<h2> O desafio consiste em: </h2>
<ul>
    <li> 
        <p style="text-align: justify; font-size: 1.2em;">
        Criar uma imagem Docker Golang.
        </p>
    </li>
    <li> 
        <p style="text-align: justify; font-size: 1.2em;">
        Ao executar o container desta imagem, deve ser impresso na tela: "Full Cycle Rocks".
        </p>
    </li>
    <li> 
        <p style="text-align: justify; font-size: 1.2em;">
        A imagem não pode ter tamanho superior a 2MB.
        </p>
    </li>
</ul>

<h2> Resumo </h2>
<p style="text-align: justify; font-size: 1.2em;">
Criei 4 Dockerfile(s) para entender um pouco e conseguir chegar a um resultado satisfatório.</br>
Consegui chegar até <b>889k</b> com o arquivo:
</p>

[Dockerfile.004](/Dockerfile.004)

<h2> In the beginning </h2>
<p style="text-align: justify; font-size: 1.2em;">
Inicialmente, para entender um pouco como fazer esta bendita imagem, </br>
li e "meio que reproduzi" o que consegui extrair destas páginas:
</p>
<a href="https://docs.docker.com/language/golang/build-images/" target="_blank">build docker images - golang</a>

<a href="https://klotzandrew.com/blog/smallest-golang-docker-image/" target="_blank">smallest-golang-docker-image</a>

<p style="text-align: justify; font-size: 1.2em;">
O resultado foram estes arquivos:</p>

1. [main.go](/main.go)
2. [go.mod](/go.mod)
3. [Dockerfile.001](/Dockerfile.001)

<p style="text-align: justify; font-size: 1.2em;">
<i>Até então eu achava que precisava dos arquivos go.mod e main.go.</i></br>
<i>E que a organização de pastas era importante... até são, mas não nessa tarefa!</i></p>

<p style="text-align: justify; font-size: 1.2em;">
Comando para construir a imagem:</p>

```code
$ docker build -t mysmallgoimage:v1.0 -f Dockerfile.001 .
 [+] Building 1.9s (13/13) FINISHED 
```

<p style="text-align: justify; font-size: 1.2em;">
Conferir a imagem criada:</p>

```code
$ docker images
 REPOSITORY      TAG   IMAGE ID      CREATED        SIZE
 mysmallgoimage  v1.0  5df3f361c57f  5 seconds ago  770MB
```

<p style="text-align: justify; font-size: 1.2em;">
Ainda que com tamanho absurdo, confere o funcionamento:</p>

```code
$ docker run --rm mysmallgoimage:v1.0
 Full Cycle Rocks!!!
```

<h2>Let there be <i>Multistage</i></h2>
<p style="text-align: justify; font-size: 1.2em;">
Ainda na página que falava sobre o build do docker Go, se explicava o Multistage.</br>
E assim eu prossegui, obtendo o:</p>

[Dockerfile.001-multistage](/Dockerfile.001-multistage)

<p style="text-align: justify; font-size: 1.2em;">
<i>Watch out! Spoiler</i> - nova imagem com 22MB</br>
Vamos lá...</p>

```code
$ docker build -t mysmallgoimage:v1.0-multi -f Dockerfile.001-multistage 
 [+] Building 5.1s (18/18) FINISHED 
```

<p style="text-align: justify; font-size: 1.2em;">
Conferir a imagem criada:</p>

```code
$ docker images
 REPOSITORY      TAG         IMAGE ID      CREATED        SIZE
 mysmallgoimage  v1.0-multi  9a29d52af2c5  4 seconds ago  22MB
```

```code
$ docker run --rm mysmallgoimage:v1.0-multi
 Full Cycle Rocks!!!
```

<h2>And one tried <i>Multistage</i> and saw that it is good</h2>
<p style="text-align: justify; font-size: 1.2em;">
Tudo que o homem cria pode sempre ficar melhor, somado ao fato de que eu não sei nem 1% (por assim dizer) sobre tudo isso que estou fazendo aqui, continuei lendo, e na página <a href="https://docs.docker.com/build/building/multi-stage/" target="_blank">building multi-stage</a> descobri que podia incluir o código da função direto no dockerfile e não precisava incluir o módulo, e também acabei descobrindo que a <i>scratch</i> é a imagem que vinha de encontro à necessidade de redução do tamanho.</br>
Sendo assim, prossegui, obtendo o seguinte arquivo:</p>

[Dockerfile.002](/Dockerfile.002)

<p style="text-align: justify; font-size: 1.2em;">
Objetivo atingido!</p>

```code
$ docker build -t mysmallgoimage:v2.0 -f Dockerfile.002 .
 [+] Building 4.9s (14/14) FINISHED 
```

<p style="text-align: justify; font-size: 1.2em;">
Imagem criada com tamanho inferior a 2MB:</p>

```code
$ docker images
 REPOSITORY      TAG   IMAGE ID      CREATED         SIZE
 mysmallgoimage  v2.0  b4ad364e7060  35 seconds ago  1.81MB
```

```code
$ docker run --rm mysmallgoimage:v2.0
 Full Cycle Rocks!!!
```

<h2>And one kept reading and the evening and the night were the first day</h2>
<p style="text-align: justify; font-size: 1.2em;">
 Continuei lendo, e achei esse tal de <a href="https://aprendagolang.com.br/como-diminuir-o-tamanho-da-sua-aplicacao-com-ldflags/" target="_blank">ldflags</a> que ajudou a reduzir o binário.</br>
 A mudança foi pequena</p>
 
[Dockerfile.003](/Dockerfile.003)

```code
$ docker build -t mysmallgoimage:v3.0 -f Dockerfile.003 .
 [+] Building 2.8s (14/14) FINISHED 
```

```code
$ docker images
 REPOSITORY      TAG   IMAGE ID      CREATED         SIZE
 mysmallgoimage  v3.0  4e8947734e5e  7 seconds ago   1.18MB
```

```code
$ docker run --rm mysmallgoimage:v3.0
 Full Cycle Rocks!!!
```

---

<p style="text-align: justify; font-size: 1.2em;">
 Ainda retirei o módulo de formatação e tentei usar a imagem alpine como origem, e com isso ainda obtive mais um pouco de redução no tamanho.</p>
 
[Dockerfile.004](/Dockerfile.004)

```code
$ docker build -t mysmallgoimage:v4.0 -f Dockerfile.004 .
 [+] Building 3.2s (14/14) FINISHED 
```

```code
$ docker images
 REPOSITORY      TAG   IMAGE ID      CREATED         SIZE
 mysmallgoimage  v4.0  10a317d99a3e  3 minutes ago   889kB

```

```code
$ docker run --rm mysmallgoimage:v4.0
 Full Cycle Rocks!!!
```
---
<h3> That's All Folks </h3>
