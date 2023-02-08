# Jest

## Introdução

Jest é uma das ferramentas de testes mais populares e amplamente utilizadas para aplicações feitas em React. Com sua simplicidade e flexibilidade, ele permite que você crie testes unitários, integração e snapshots para sua aplicação de forma eficiente e confiável. Além disso, Jest é altamente integrado ao React, o que significa que você pode escrever testes que simulam interações com componentes de forma fácil e precisa.

Neste texto, vamos explorar como utilizar Jest como ferramenta de testes para uma aplicação feita em React. Vamos aprender como escrever testes unitários e integração para garantir que nossa aplicação esteja funcionando corretamente, bem como como utilizar snapshots para facilitar o desenvolvimento e garantir a qualidade do código. Ao final deste texto, você terá uma compreensão sólida sobre como utilizar Jest para testar sua aplicação React e garantir que ela esteja funcionando corretamente.

Iremos criar uma aplicação React simples para demonstrar como utilizar Jest para testar uma aplicação React. Você pode encontrar o código-fonte desta aplicação no repositório do GitHub.

## Requisitos

- Node.js
- NPM ou Yarn
- Conhecimentos básicos de React

## Criando uma aplicação React

Para começar, vamos criar uma aplicação React com Typescript simples utilizando o create-react-app. Para isso, execute o seguinte comando no terminal:

```bash
yarn create react-app my-app --template typescript
```

Após a criação da aplicação, vamos instalar as dependências necessárias para utilizar o Jest. Para isso, execute o seguinte comando no terminal:

```bash
yarn add -D jest @types/jest ts-jest @testing-library/react
```

Ao criar o app, teremos a seguinte estrutura de pastas:

```bash
my-app
├── README.md
├── node_modules
├── package.json
├── .gitignore
├── public
└── src
    ├── App.css
    ├── App.test.tsx
    ├── App.tsx
    ├── index.css
    ├── index.tsx
    ├── logo.svg
    ├── react-app-env.d.ts
    ├── reportWebVitals.ts
    ├── setupTests.ts
    └── serviceWorker.ts
```

A pasta src contém o código-fonte da nossa aplicação. Dentro desta pasta, temos o arquivo App.tsx, que é o componente principal da nossa aplicação. O arquivo App.test.tsx contém os testes unitários para o componente App. O arquivo setupTests.ts é responsável por configurar o ambiente de testes do Jest. Por fim, a pasta public contém os arquivos estáticos da aplicação, como o index.html e o favicon.

## Configurando o Jest

Para configurar o Jest, precisamos criar um arquivo jest.config.js na raiz do projeto. Este arquivo será responsável por configurar o Jest para que ele possa ser utilizado para testar nossa aplicação React. O conteúdo deste arquivo é o seguinte:

```js
module.exports = {
  testEnvironment: "jsdom",
  rootDir: ".",
  modulePaths: ["<rootDir>"],
  moduleDirectories: ["node_modules", "src"],
  setupFilesAfterEnv: ["<rootDir>/setupJest.js"],
};
```

Neste arquivo, estamos exportando um objeto com as configurações do Jest com os seguintes parâmetros:

O primeiro parâmetro que precisamos configurar é o testEnvironment. Este parâmetro define o ambiente de testes que o Jest irá utilizar. O valor jsdom é o ambiente de testes padrão do Jest, que simula o DOM do navegador. Este é o ambiente de testes que utilizaremos para testar nossa aplicação React.

O segundo parâmetro que precisamos configurar é o rootDir. Este parâmetro define o diretório raiz do projeto. O valor . indica que o diretório raiz do projeto é o diretório atual.

O terceiro parâmetro que precisamos configurar é o modulePaths. Este parâmetro define os caminhos que o Jest irá utilizar para encontrar os módulos. O valor <rootDir> indica que o Jest irá utilizar o diretório raiz do projeto para encontrar os módulos.

O quarto parâmetro que precisamos configurar é o moduleDirectories. Este parâmetro define os diretórios que o Jest irá utilizar para encontrar os módulos. O valor node_modules indica que o Jest irá utilizar o diretório node_modules para encontrar os módulos. O valor src indica que o Jest irá utilizar o diretório src para encontrar os módulos.

O quinto parâmetro que precisamos configurar é o setupFilesAfterEnv. Este parâmetro define os arquivos que o Jest irá executar após a inicialização do ambiente de testes. O valor <rootDir>/setupJest.js indica que o Jest irá executar o arquivo setupJest.js após a inicialização do ambiente de testes.

```js
describe("Teste do componente App", () => {
  it("Deve renderizar o componente App", () => {
    const { getByText } = render(<App />);
    expect(getByText("Hello World")).toBeInTheDocument();
  });
});
```
O teste acima é um teste unitário para o componente App. O teste consiste em renderizar o componente App e verificar se o texto "Hello World" está presente na tela. Para executar o teste, execute o seguinte comando no terminal:

```bash
yarn test
```

```js
jest.mock("../state/hook/useListaDeParticipantes", () => {
  return {
    useListaDeParticipantes: jest.fn(),
  };
});

const mockNavegacao = jest.fn();

jest.mock("react-router-dom", () => {
  return {
    useNavigate: () => mockNavegacao,
  };
});
```
Aqui estamos definindo mocks para os hooks useListaDeParticipantes e useNavigate. O hook useListaDeParticipantes é um hook que retorna a lista de participantes. O hook useNavigate é um hook que retorna uma função para navegar para uma determinada rota. Como não queremos testar o comportamento destes hooks, vamos definir mocks para estes hooks. O mock do hook useListaDeParticipantes irá retornar uma lista vazia. O mock do hook useNavigate irá retornar uma função vazia.


<!-- explique os testes abaixo -->

```js
describe("quando não existem participantes suficientes", () => {
  beforeEach(() => {
    (useListaDeParticipantes as jest.Mock).mockReturnValue([]);
  });
  test("a brincadeira não pode ser iniciada", () => {
    render(
      <RecoilRoot>
        <Rodape />
      </RecoilRoot>
    );
    const botao = screen.getByRole("button");
    expect(botao).toBeDisabled();
  });
});

describe("quando existem participantes suficientes", () => {
  beforeEach(() => {
    (useListaDeParticipantes as jest.Mock).mockReturnValue([
      "Ana",
      "Catarina",
      "Josefina",
    ]);
  });
  test("a brincadeira pode ser iniciada", () => {
    render(
      <RecoilRoot>
        <Rodape />
      </RecoilRoot>
    );
    const botao = screen.getByRole("button");
    expect(botao).not.toBeDisabled();
  });
  test("a brincadeira foi iniciada", () => {
    render(
      <RecoilRoot>
        <Rodape />
      </RecoilRoot>
    );
    const botao = screen.getByRole("button");
    fireEvent.click(botao);

    expect(mockNavegacao).toHaveBeenCalledTimes(1);
    expect(mockNavegacao).toHaveBeenCalledWith("/sorteio");
  });
});

```

```js
const mockNavegacao = jest.fn();

jest.mock("react-router-dom", () => {
  return {
    useNavigate: () => mockNavegacao,
  };
});

describe("a pagina de configuracao", () => {
  test("deve ser renderizada corretamente", () => {
    const { container } = render(
      <RecoilRoot>
        <Configuracao />
      </RecoilRoot>
    );

    expect(container).toMatchSnapshot();
  });
});
```

To make performance tests on a ReactJS app, you can use the performance testing tools provided by Jest, React, and other testing libraries. Here is a general outline of the process:

Identify performance-critical parts of your ReactJS app: This could include heavy data processing, complex UI interactions, etc.

Use the React's useEffect hook to measure the time it takes for a component to render and update. You can use the performance.mark and performance.measure methods to create a performance measurement and report it in the browser's developer tools.

Set up Jest tests to measure the performance of these components. You can use Jest's test method to write tests that measure the performance of your components and compare them against performance thresholds.

Use Jest's beforeAll and afterAll methods to run the performance tests before and after each test run, to ensure that the performance tests don't interfere with other tests.

Use Jest's expect method to make assertions about the performance of your components. For example, you could expect that a component takes no longer than a certain amount of time to render, or that its performance does not degrade over time.

Continuously monitor the performance of your ReactJS app using Jest and other performance testing tools.

Optimize the performance of your ReactJS app by making changes based on the results of your performance tests.

It's important to note that performance testing can be a complex and time-consuming process, and it's best to approach it incrementally, starting with the most critical parts of your app and expanding your performance tests as needed.

1.  jest: Runs all tests in your project.

2.  jest <filename>: Runs all tests in a specific file.

3.  jest <testName>: Runs a specific test in your project.

4.  jest --watch: Runs Jest in watch mode, re-running tests whenever a file changes.

5.  jest --coverage: Generates a coverage report for your tests.

6.  jest --verbose: Provides more detailed information about the tests that are run.

7.  jest --no-cache: Runs Jest without using its cache, ensuring that all tests are run from scratch.

8.  jest --updateSnapshot: Updates Jest snapshots for tests that use the toMatchSnapshot assertion.

9.  jest --runInBand: Runs all tests serially in the same process, rather than spreading them across multiple processes.

10. jest --bail: Stops running tests after the first failure.

This is just a small selection of the commands that are available with Jest. You can find more information on Jest's official documentation.
