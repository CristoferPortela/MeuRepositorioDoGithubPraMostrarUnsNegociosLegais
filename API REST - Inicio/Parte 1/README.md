
# Consumindo APIs REST com Fetch: GET e POST

Parte [1/5]



Assim como disse na última postagem, estou iniciando uma pequena serie de artigos aqui no LinkedIn que sobre o básico do consumo de APIs, então aqui vai o primeiríssimo assunto, requisições básicas com Javscript usando os métodos GET e POST.

A função fetch() (Que é nativa do javascript) permite fazer requisições HTTP de forma bem simples, o que torna ela perfeita para iniciarmos a entender melhor sobre o uso de APIs, então vamos lá!  

Quando se fala em API, normalmente pensamos apenas nas requisições web, por exemplo ao fazer login em um determinado site, ou para buscar algum conteúdo, mas as APIs não se limitam apenas a isso, uma api basicamente é uma maneira de dois sistemas "conversarem", os componentes do seu computador tem sua api para se comunicar com outros componentes, os precessadores (Intel, Ryzen, etc.), por exemplo tem uma API e suporta algumas APIs de outros componentes e softwares. Entretanto, nessa série abordarei apenas as requisições HTTP, mas saiba, API é muito mais que apenas isso!  

Para iniciar com a função fetch() vamos usar uma API bem simples, a API de busca de CEPs. Por quê? Por eles disponibilizam um serviço bem legal e útil para usarmos e é uma ótima maneira de iniciar.  

Toda vez que entramos num site pela barra de um navegador, já estamos fazendo uma requisição! Especificamente uma requisição do método GET e para buscar requisições GET com o método fetch() é tão simples quanto buscar algo no navegador. Veja só:

```
fetch('https://viacep.com.br/ws/01001000/json/')
```



No lugar de 01001000, coloque qualquer cep existente no Brasil e você verá as informações relativas a esse endereço, por exemplo o cep 01001000 é da praça da Sé (só lembrando, evite colocar informações pessoais importantes em locais públicos).  

Continuando, precisamos acessar essa informação agora. Fizemos a requisição, mas para acessamos o que veio dela? É muito simples:  
``` 
fetch('https://viacep.com.br/ws/01001000/json/')
    .then((data) => data.json())
```

A classe fetch() retorna uma Promise, ou seja, o resultado não vai mostrar logo após ser executado, por quê? Isso é uma ação assíncrona e serve para caso você faça uma requisição que demore. Se esse foi o caso, seu site/app/software não vai travar até que a requisição seja finalizada, então você pode continuar com outras ações logo em seguida, em breve vou mostrar isso melhor.  

Será que apenas fazer isso que mostrei acima já é o suficiente para acessar as informações? Ainda não, isso ```data.json()``` retorna outra Promise, então precisamos de mais um .then()

```
fetch('https://viacep.com.br/ws/01001000/json/')
    .then((data) => data.json())
    .then((data) => console.log(data))
```

Agora conseguimos acessar as informações!! 🎉🎉  ```console.log(data)``` nos permite visualizar isso no console, mas podemos fazer o que quiser com a informação, por exemplo, se estiver usando React

```` 
const [cep, setCep] = useState()

const onClickHandle = () => {
    fetch('https://viacep.com.br/ws/01001000/json/')
        .then((data) => data.json())
        .then((data) => setCep(data))

}

return (... resto do código ...)
````

Aqui pegamos a informação e salvamos ela em outra variável. 

Agora que acessamos a informação, vamos voltar um pouco para a explicação da Promise, apenas para você ver como ele funciona, faça o seguinte:

```
fetch('https://viacep.com.br/ws/01001000/json/')
    .then((data) => data.json())
    .then ((data) => console.log(data))

console.log('oi')
```
Quem vai aparecer primeiro? Se não fosse uma Promise, obviamente, o console.log(data), mas essa parte é executada somente depois da requisição finalizar, ela demora apenas alguns milissegundos, mas isso é o suficiente para a linha seguinte ser executada antes, então o 'oi' vai aparecer primeiro!  

Mas se eu não quiser fazer assim? Eu quero continuar o meu código normal, então vamos declarar uma função async


```
const getCep = async () => {

// Fazemos a variavel aqui com um await após o sinal de igual
let cep = await fetch('https://viacep.com.br/ws/01001000/json/')
.then((data) => data.json())
// Agora não precisamos colocar o segundo .then()
// E mostramos no console a informação que acabamos de pegar da nossa API
console.log(cep)
console.log('oi') // agora essa linha será a última a ser executada
}
// Executamos a função
getCep()
```

O - await - serve para esperarmos a Promise trazer o seu resultado para só então seguir adiante, mas ela DEVE estar dentro de uma função declarada com async

```
// Para funções "normais"
async function minhaFuncao() {...}

// Para funções declaradas numa varaivel
const minhaFuncao = async () => {...}

Você pode fazer isso em qualquer lugar, até mesmo...

let cep = await fetch('https://viacep.com.br/ws/01001000/json/')
.then(async (data) => {
    const minhaInfo = await data.json()
    console.log(minhaInfo)
})
```

Essa última pode parecer meio estranho, mas funciona!  

Aliás, isso aqui também funciona!

```
async function getCep() {
    let cepPromise = await fetch('https://viacep.com.br/ws/01001000/json/')
    let cep = await cepPromise.json()
    console.log(cep)
}
getCep()
```


## Uhuulll quase lá! Só mais um pouco para finalizarmos a primeira etapa dessa série


Falamos sobre uma requisição GET, agora vamos falar do método POST.

Atualmente se fala muito de API REST, e não é à toa, elas são muito importantes! Resumindo, as API REST são um modelo de arquitetura, sugiro fortemente pesquisar mais sobre o tema, ele é muito importante!

Qual a diferença entre GET e POST?
Enquanto GET é usado para buscar dados, o método POST é geralmente utilizado para enviar informações para uma API, como dados de formulários ou novos registros.

Nesse exemplo não vou usar a api do correio, mas imagine que você vai salvar as informações de um usuário recém cadastrado no sistema.  Aqui está um exemplo de como fazer isso com fetch():

```
fetch('https://api.example.com/create-user', {
  method: 'POST',                      // Define o método POST
  headers: {
    'Content-Type': 'application/json'  // Define o tipo de conteúdo como JSON
  },
  body: JSON.stringify({                // Envia os dados no corpo da requisição
    name: 'John Doe',
    email: 'john@example.com',
    password: 'MinhaBelaSenhaSuperSegura123',
  })
})
  .then(response => response.json())    // Converte a resposta para JSON
  .then(data => console.log('Sucesso:', data))  // Manipula a resposta
  .catch(error => console.error('Erro:', error)); // Caso dê erro, você pode tratar ele aqui!!!
```

Uaauu, quanta informação nova! Vamos ver parte por parte?

O segundo parametro do fetch() é um objeto com algumas propriedades:

```
fetch(url, {
    method: 'POST',
})
```

É aqui onde definimos o método, por padrão, se eu não definir nada, o método será GET, mas existem outras opções que você pode usar, [deixarei a referencia do MDN aqui](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Methods).


Em seguida:

```
{
...
  headers: {
    'Content-Type': 'application/json'
  },
...
}
```

O que é isso? Um header (cabeçalho em português) define informações sobre a requisição, sabe a tag ```<head></head>``` do HTML? É tipo isso.  
Existem uma grande quantidade de propriedades que podem ir aqui, mas por hora vamos falar de duas que eu considero principais:

- Content-Type: Define o tipo de conteúdo, nesse caso é um json que vou enviar, então ele será um 'application/json', se eu for enviar um css, seria 'text/css' um mp4 seria: 'video/mp4', leia este artigo para saber mais: [Content Type no MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type).
- Authorization: Ele normalmente carrega um token com uma string de caracteres gerados pelo servidor, ela serve para o usuário autenticado poder acessar a página, é como um cracha de identificação, por exemplo, eu vou acessar minha conta de usuário, se eu tiver a authorization, eu posso alterar as coisas, mas se não tiver, eu vou ser redirecionado para a página inicial, por exemplo.


Esse segundo parametro que eu citei (Authorization) é muito usado até junto de requisições GET para pegar informações de um usuário autenticado:

```
fetch('minha-rota', {Authorization: userInfo.getToken()})
```

Nesse exemplo eu tenho o token salvo e ele é acessado por essa classe UserInfo no método getToken().


---

Seguindo adiante...

```
// Agora a parte que vai enviar os dados
body: JSON.stringify({               
    name: 'John Doe',
    email: 'john@example.com',
    password: 'MinhaBelaSenhaSuperSegura123',
  })
```

A propriedade body é onde usamos para enviar as informações para o servidor. ATENÇÃO, requests no tipo GET, não tem body, você deve usar outros métodos para enviar informações com o body, em outro artigo posso falar sobre, mas se tiver interesse em saber agora, [veja sobre aqui](https://pt.stackoverflow.com/questions/401582/a-diferen%C3%A7a-entre-req-params-e-req-query).

No exemplo anterior, usei uma classe nativa do javascript para "jsonificar" meu object javascript, a JSON.stringify(), nela você passa um objeto e ele é trasnformado numa string formatada como json.  
  

E agora para finalizarmos tudo por hoje:

```
...
  .then(response => response.json())    // Converte a resposta para JSON
  .then(data => console.log('Sucesso:', data))  // Manipula a resposta
  .catch(error => console.error('Erro:', error)); // Caso dê erro, você pode tratar ele aqui!!!
```

Já falamos sobre os dois .then(), mas agora temos uma nova: .catch(), ele é similar a um bloco try{} catch(){}, caso dê algum erro na requisição, (caso o servidor retorne status com erro 40* ou 5**, ele não caí nesse erro, somente se tiver erros como de Network)  


## O fim...


Puxa, já ficou longo demais esse artigo, tem quase 10.000 caracteres, mas ainda falta muito para vermos, porém, aqui temos o básico do conhecimento sobre requisições HTTP, no futuro estaremos falando sobre outros temas importantes, não perca o próximo artigo! Veremos sobre como facilitar ainda mais as requisições com axios
