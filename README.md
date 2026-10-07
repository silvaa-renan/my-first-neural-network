# My First Neural Network

**Criando e treinando minha primeira rede neural com JavaScript e TensorFlow.js.**

Projeto de estudo que demonstra a preparação de dados, o treinamento de uma rede neural e a classificação de uma nova entrada em três categorias: `premium`, `medium` e `basic`.

O exemplo utiliza idade, cor e localização como atributos. As categorias são rótulos didáticos atribuídos manualmente: o projeto não estabelece uma regra real de classificação de pessoas.

## Tecnologias

- JavaScript com ES Modules
- Node.js e npm
- TensorFlow.js para Node.js (`@tensorflow/tfjs-node`)

## Como funciona

1. Representa os atributos como números.
2. Normaliza a idade e codifica cor e localização com *one-hot encoding*.
3. Cria uma rede sequencial com duas camadas densas.
4. Treina o modelo durante 100 épocas.
5. Calcula as probabilidades das três categorias para uma nova entrada.
6. Exibe as categorias em ordem decrescente de probabilidade.

## Dados de treinamento

O conjunto de treinamento contém apenas três exemplos:

| Pessoa | Idade | Cor | Localização | Categoria |
| --- | --- | --- | --- | --- |
| Erick | 30 | Azul | São Paulo | premium |
| Ana | 25 | Vermelho | Rio | medium |
| Carlos | 40 | Verde | Curitiba | basic |

### Vetor de entrada

Cada pessoa é representada por sete valores, nesta ordem:

```text
[idade_normalizada, azul, vermelho, verde, São Paulo, Rio, Curitiba]
```

A idade usa normalização min-max, com os limites do treino:

```text
idade_normalizada = (idade - 25) / (40 - 25)
```

Por exemplo, 28 anos corresponde a `0.2`. Os mesmos limites devem ser utilizados na previsão. No código, a idade normalizada de Erick foi arredondada para `0.33`.

Cor e localização usam one-hot encoding: cada opção tem uma posição, marcada com `1` quando está presente e `0` nas demais.

### Rótulos de saída

| Categoria | Vetor |
| --- | --- |
| premium | `[1, 0, 0]` |
| medium | `[0, 1, 0]` |
| basic | `[0, 0, 1]` |

## Arquitetura e treinamento

| Componente | Configuração |
| --- | --- |
| Entrada | 7 atributos |
| Camada oculta | 80 neurônios com ReLU |
| Camada de saída | 3 neurônios com Softmax |
| Otimizador | Adam |
| Função de perda | `categoricalCrossentropy` |
| Métrica | `accuracy` |
| Épocas | 100 |
| Embaralhamento | `shuffle: true` |

A ReLU aplica `max(0, x)` à soma ponderada recebida por cada neurônio. A Softmax transforma as saídas em valores positivos cuja soma é 1, interpretados como probabilidades das classes.

Durante o treinamento, o callback imprime a perda (`loss`) ao final de cada época. Embora a acurácia esteja configurada como métrica, o callback fornecido exibe apenas a perda. A numeração das épocas começa em zero.

## Como executar

As instruções abaixo assumem que o código será salvo como **`index.js`**, na raiz do projeto, e que Node.js e npm já estão instalados no ambiente. No WSL, execute os comandos no terminal Linux.

### 1. Configure o projeto

Dentro da pasta `my-first-neural-network`, caso ainda não exista um `package.json`:

```bash
npm init -y
```

Configure ES Modules e instale a dependência:

```bash
npm pkg set type=module
npm install @tensorflow/tfjs-node
```

O pacote utiliza componentes nativos. A instalação depende da compatibilidade entre a versão do Node.js, a plataforma e a versão do pacote; consulte a documentação oficial em caso de falha.

### 2. Prepare o código

Salve o script em `index.js`. A forma de importação utilizada na documentação do TensorFlow.js é:

```javascript
import * as tf from '@tensorflow/tfjs-node';
```

Se estiver copiando de um texto formatado, remova os asteriscos ao redor dos parâmetros, as entidades `&#x20;` e as barras invertidas inseridas antes de crases ou operadores. Esses elementos de formatação não devem aparecer no JavaScript.

### 3. Execute

```bash
node index.js
```

O terminal exibirá a perda de cada época e, em seguida, as três categorias com seus percentuais, ordenadas do maior para o menor.

Os resultados podem variar entre execuções devido à inicialização dos pesos e ao embaralhamento. Não há um percentual de referência validado para este projeto.

## Exemplo de previsão: atenção à codificação

A pessoa declarada no código é:

```javascript
const pessoa = {
  nome: 'Ze',
  idade: 28,
  cor: 'verde',
  localizacao: 'Curitiba'
};
```

Porém, o vetor original `[0.2, 1, 0, 0, 1, 0, 0]` representa **28 anos, azul e São Paulo**. O objeto `pessoa` não é convertido automaticamente: a previsão utiliza o vetor numérico passado para a função.

Para representar os atributos de Ze, substitua o vetor por:

```javascript
const pessoaTensorNormalizado = [
  [0.2, 0, 0, 1, 0, 0, 1]
];
```

## Limitações do experimento

- Três exemplos, com apenas um por classe, são insuficientes para avaliar generalização.
- Aumentar a quantidade de neurônios não compensa a falta de dados e pode facilitar a memorização do treino.
- Não há separação entre conjuntos de treinamento, validação e teste.
- Uma perda baixa no treino ou uma probabilidade alta na saída não garante uma previsão correta.
- O modelo é treinado novamente a cada execução; o script não salva os pesos.
- O pré-processamento é manual e depende da manutenção da mesma ordem dos atributos.

## Próximos passos

- Ampliar o conjunto de dados e definir critérios claros para os rótulos.
- Automatizar a normalização e o one-hot encoding.
- Separar dados para treinamento, validação e teste.
- Comparar arquiteturas menores e diferentes quantidades de épocas.
- Salvar e carregar o modelo treinado.
- Liberar tensores com `dispose()` quando não forem mais necessários.

## Referências

- [TensorFlow.js no Node.js](https://www.tensorflow.org/js/guide/nodejs)
- [Treinamento de modelos](https://www.tensorflow.org/js/guide/train_models)
- [Modelos e camadas](https://www.tensorflow.org/js/guide/models_and_layers)

