# 2024-IA22-2TRi
abra o seu github e logue sua conta
a aprtir do inicio aperte o mais(+), clique no new repository
de um nome no repository name e em baixo marque o campo add a readme file
vai em <> code no canto superior direito, va para a divisoria apertando codespace e aperte em create codespace on main
siga ate o terminal em baixo da tela e aperte,
no campo texto coloque o texto a seguir
    npm init -y
    npm install express cors sqlite3 sqlite
    npm install --save-dev typescript nodemon ts-node @types/express @types/cors
    npx tsc --init
    mkdir src
    touch src/app.ts
clique em permitir na parte superior esquerda, e em seguida no botao colar, espere rodar todos os codigos...
va no arquivo tsconfig.json do lado direito
vai até aproximadamente a linha 58 aonde estiver escrito( // "outDir": "./dist", )aperte para pular a linha e na linha livre abaixo coloque 
    "outDir":"./dist",
pule novamente uma linha e na linha livre abaixo coloque
    //"rootDir": "./src",
vai até package.json no lado esquerdo, va ate a linha 6 que estara escrito  ( "scripts": { ) na linha de baixo delete o que esta escrito e coloque
    "dev": "npx nodemon src/app.ts"
clique no botao src até a fecha ficar para baixo, vai no nome do seu repositorio acima do node_modules e passe o mouse em cima ate aparecer um simbolo de papel com um + aonde você criara um arquivo com o nome app.ts,(ao fazer o arquivo certifique se ele esta na pasta src apertando o botão src ate ele ficar com a flecha de lado e assim sumindo o a pasta app.ts, para aparecer novamente aperte em src ), em seguida copie o seguinte texto
    import express from 'express';
    import cors from 'cors';

    const port = 3333;
    const app = express();

    app.use(cors());
    app.use(express.json());

    app.get('/', (req, res) => {
        res.send('Hello World');
    });

    app.listen(port, () => {
        console.log(`Server running on port ${port}`);
    });
em seguida vai ao terminal e escreva npm run dev, espere até aparecer a seguinte mensagem (Server running on port 3333) no terminal 
pode aperta no potao de abrir no navegador na aba do lado inferior esquerdo, onde abrirá uma nova página que estara escrito hello world
seguindo os passos do app.ts crie um novo arquivo com o nome de database.ts e coloque 
    import { open, Database } from 'sqlite';
    import sqlite3 from 'sqlite3';

    let instance: Database | null = null;

    export async function connect() {
        if (instance) return instance;

    const db = await open({
        filename: './src/database.sqlite',
        driver: sqlite3.Database
     });
  
     await db.exec(`
        CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
         name TEXT,
        email TEXT
        )
  ` );

    instance = db;
    return db;
}
em seguida vai para a area lateral esquerda aonde vc apertara no quadrado com um quadradinho para fora na barra de pesquisa e escreva rest, espere até aparecer o o app rest client e instale
apos instalar altere o app.ts deletando o para colocar
     import express from 'express';
    import cors from 'cors';
    import { connect } from "./database"

    const port = 3333;
    const app = express();

    app.use(cors());
    app.use(express.json());

    app.get('/', (req, res) => {
        res.send('Hello World');
    });

    app.listen(port, () => {
        console.log(`Server running on port ${port}`);
    });
abaixo disso coloque 
    app.get('/users', async (req, res) => {
        const db = await connect();
        const users = await db.all('SELECT * FROM users');
        res.json(users);
    });
fazendo um get
abaixo disso coloque 
    app.post('/users', async (req, res) => {
        const db = await connect();
        const { name, email } = req.body;
        const result = await db.run('INSERT INTO users (name, email) VALUES (?, ?)', [name, email]);
        const user = await db.get('SELECT * FROM users WHERE id = ?', [result.lastID]);
        res.json(user);
    });
fazendo um post
abaixo disso coloque 
    app.put('/users/:id', async (req, res) => {
        const db = await connect();
        const { name, email } = req.body;
        const { id } = req.params;
         await db.run('UPDATE users SET name = ?, email = ? WHERE id = ?', [name, email, id]);
         const user = await db.get('SELECT * FROM users WHERE id = ?', [id]);
        res.json(user);
    });
fazendo um put
abaixo disso coloque
    app.delete('/users/:id', async (req, res) => {
        const db = await connect();
        const { id } = req.params;
        await db.run('DELETE FROM users WHERE id = ?', [id]);
        res.json({ message: 'User deleted' });
    });
após isso crie uma pasta de teste chamada de test.http seguindo so passos do app.ts porém teste criar no node_modules
nesta pasta vc colocara
    POST https://miniature-space-spoon-jv56p5jv4qjcwqr-3333.app.github.dev/users HTTP/1.1
    content-type: application/json

    {
        "name": "John Doe",
        "email": "johndoe@mail.com"
    }

    ####

    PUT http://localhost:3333/users/1 HTTP/1.1
    content-type: application/json

    {
        "name": "John Doe Updated",
        "email": ""
    }

    ####

    DELETE http://localhost:3333/users/1 HTTP/1.1
    em seguida crie um arquivo igual ao app.ts no src e coloque o nome de database.ts, em seguida copie esta parte 
      import { open, Database } from 'sqlite';
    import sqlite3 from 'sqlite3';

    let instance: Database | null = null;

    export async function connect() {
        if (instance) return instance;

     const db = await open({
        filename: './src/database.sqlite',
        driver: sqlite3.Database
    });
  
     await db.exec(`
        CREATE TABLE IF NOT EXISTS users (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            name TEXT,
            email TEXT
         )
    `);

        instance = db;
        return db;
}
para o promixo passo altere o app.ts(delete o que esta escrito) e coloque
    import express from 'express'
    import cors from 'cors'
    import { connect } from './database'

    const port = 3333
    const app = express()

    app.use(cors())
    app.use(express.json())
    app.use(express.static(__dirname + '/../public'))

    app.get('/users', async (req, res) => {
        const db = await connect()
        const users = await db.all('SELECT * FROM users')
        res.json(users)
    })

    app.post('/users', async (req, res) => {
        const db = await connect()
        const { name, email } = req.body
        const result = await db.run('INSERT INTO users (name, email) VALUES (?, ?)', [name, email])
        const user = await db.get('SELECT * FROM users WHERE id = ?', [result.lastID])
        res.json(user)
    })

    app.put('/users/:id', async (req, res) => {
        const db = await connect()
        const { name, email } = req.body
        const { id } = req.params
        await db.run('UPDATE users SET name = ?, email = ? WHERE id = ?', [name, email, id])
        const user = await db.get('SELECT * FROM users WHERE id = ?', [id])
        res.json(user)
    })

    app.delete('/users/:id', async (req, res) => {
        const db = await connect()
         const { id } = req.params
        await db.run('DELETE FROM users WHERE id = ?', [id])
        res.json({ message: 'User deleted' })
    })

    app.listen(port, () => {
        console.log(`Server running on port ${port}`)
    })
agora crie uma pasta public, va ate a primeira flecha acima do node_modules com o mouse até esta parte e ao lado de onde vc criou os arquivos e aperte o do lado direito apertando ele, após isso crie um arquivo dentro do public com o nome index.html coloque dentro dele o que vem a seguir
    <!DOCTYPE html>
    <html lang="en">

    <head>
    <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
    title>Document</title>
    </head>

    <body>
        <form>
        <input type="text" name="name" placeholder="Nome">
        <input type="email" name="email" placeholder="Email">
        <button type="submit">Cadastrar</button>
    </form>

    <table>
        <thead>
        <tr>
            <th>Id</th>
            <th>Name</th>
            <th>Email</th>
            <th>Ações</th>
            </tr>
        </thead>
        <tbody>
            <!--  -->
         </tbody>
     </table>

    <script>
        // 
        const form = document.querySelector('form')

        form.addEventListener('submit', async (event) => {
            event.preventDefault()

        const name = form.name.value
        const email = form.email.value

        await fetch('/users', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ name, email })
        })

            form.reset()
            fetchData()
        })

        // 
        const tbody = document.querySelector('tbody')

        async function fetchData() {
            const resp = await fetch('/users')
            const data = await resp.json()

            tbody.innerHTML = ''

        data.forEach(user => {
            const tr = document.createElement('tr')
            tr.innerHTML = `
                <td>${user.id}</td>
                <td>${user.name}</td>
                <td>${user.email}</td>
                 <td>
                    <button class="excluir">excluir</button>
                    <button class="editar">editar</button>
                </td>
        `

                const btExcluir = tr.querySelector('button.excluir')
                const btEditar = tr.querySelector('button.editar')

                btExcluir.addEventListener('click', async () => {
                await fetch(`/users/${user.id}`, { method: 'DELETE' })
                tr.remove()
                })

                btEditar.addEventListener('click', async () => {
                const name = prompt('Novo nome:', user.name)
                const email = prompt('Novo email:', user.email)

                await fetch(`/users/${user.id}`, {
                method: 'PUT',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ name, email })
                })

                fetchData()
                })

                tbody.appendChild(tr)
            })
            }

         fetchData()
        </script>
        </body>

        </html>