# Arbitragem de Moedas e Detecção de Ciclos Negativos

## Introdução

Arbitragem é uma estratégia financeira que envolve a compra e venda simultânea de ativos para lucrar com diferenças de preço entre mercados ou instrumentos financeiros. A arbitragem é possível quando há discrepâncias nos preços de um mesmo ativo ou de ativos relacionados em diferentes mercados.

### Características da Arbitragem

1. **Sem Risco (Teoricamente):**
   - A arbitragem é considerada uma estratégia sem risco porque envolve a compra e venda simultânea de ativos, eliminando a exposição ao risco de mercado.

2. **Diferenças de Preço:**
   - A arbitragem explora diferenças de preço entre mercados ou instrumentos financeiros.

3. **Eficiência de Mercado:**
   - A arbitragem ajuda a tornar os mercados mais eficientes, pois as oportunidades de arbitragem tendem a desaparecer rapidamente à medida que os traders as exploram.

### Tipos de Arbitragem

1. **Arbitragem de Moedas (Forex):**
   - Envolve a compra e venda simultânea de diferentes moedas para lucrar com discrepâncias nas taxas de câmbio.

2. **Arbitragem de Ações:**
   - Explora diferenças de preço entre ações listadas em diferentes bolsas de valores.

3. **Arbitragem de Futuros:**
   - Envolve a compra de um ativo no mercado à vista e a venda simultânea de um contrato futuro do mesmo ativo.

4. **Arbitragem de Fusões e Aquisições:**
   - Envolve a compra de ações de uma empresa-alvo e a venda de ações da empresa adquirente para lucrar com a diferença de preço durante uma fusão ou aquisição.

5. **Arbitragem Estatística:**
   - Usa modelos estatísticos para identificar discrepâncias de preço entre ativos correlacionados.

## Exemplo de Arbitragem de Moedas

Vamos supor que temos três moedas: `USD`, `EUR` e `GBP`.

- **Taxas de Câmbio:**
  - `USD` para `EUR`: 0.85
  - `EUR` para `GBP`: 0.9
  - `GBP` para `USD`: 1.4

- **Cálculo da Arbitragem:**
  - Comece com 1 USD.
  - Converta 1 USD para EUR: \( 1 \times 0.85 = 0.85 \) EUR.
  - Converta 0.85 EUR para GBP: \( 0.85 \times 0.9 = 0.765 \) GBP.
  - Converta 0.765 GBP para USD: \( 0.765 \times 1.4 = 1.071 \) USD.

- **Lucro:**
  - O lucro é de \( 1.071 - 1 = 0.071 \) USD, ou seja, 7.1% de lucro.

## Detecção de Arbitragem com Ciclos Negativos

Para detectar oportunidades de arbitragem, podemos representar as taxas de câmbio como um grafo direcionado, onde cada taxa de câmbio é uma aresta com peso negativo. A presença de um ciclo negativo no grafo indica uma oportunidade de arbitragem.

### Por que usar `-log`?

1. **Taxas de Câmbio e Arbitragem:**
   - Se temos uma taxa de câmbio de `A` para `B` como `rateAB`, isso significa que 1 unidade de `A` pode ser trocada por `rateAB` unidades de `B`.
   - Se houver um ciclo de arbitragem, podemos começar com uma unidade de uma moeda e, após várias conversões, terminar com mais de uma unidade da mesma moeda.

2. **Matemática da Arbitragem:**
   - Suponha que temos três moedas `A`, `B` e `C` com as seguintes taxas de câmbio:
     - `A` para `B`: `rateAB`
     - `B` para `C`: `rateBC`
     - `C` para `A`: `rateCA`
   - Se houver arbitragem, o produto das taxas de câmbio deve ser maior que 1:
     \[
     rateAB \times rateBC \times rateCA > 1
     \]
   - Tomando o logaritmo natural de ambos os lados:
     \[
     \log(rateAB) + \log(rateBC) + \log(rateCA) > 0
     \]
   - Invertendo o sinal:
     \[
     -\log(rateAB) -\log(rateBC) -\log(rateCA) < 0
     \]
   - Isso nos dá um ciclo negativo.

3. **Representação no Grafo:**
   - Cada taxa de câmbio é representada como uma aresta direcionada no grafo.
   - O peso da aresta é dado por `-log(rate)`.
   - Um ciclo negativo no grafo indica uma oportunidade de arbitragem.

### Exemplo de Conversão

Vamos supor que temos uma taxa de câmbio de `USD` para `EUR` como `0.85`.

- **Peso da Aresta:**
  - O peso da aresta de `USD` para `EUR` seria:
  \[
  -\log(0.85) \approx 0.1625
  \]

### Código de Exemplo

Aqui está um exemplo simples de como a conversão é feita no código:

```cpp
#include <iostream>
#include <cmath>

int main() {
    double taxaCambio = 0.85;
    double peso = -log(taxaCambio);

    std::cout << "Peso da aresta para a taxa de câmbio " << taxaCambio << " é: " << peso << std::endl;

    return 0;
}
```

### Saída Esperada

```
Peso da aresta para a taxa de câmbio 0.85 é: 0.1625
```

## Implementação Completa para Detecção de Arbitragem

Vou fornecer a implementação completa do problema de arbitragem usando o algoritmo de Floyd-Warshall.

```cpp
#include <iostream>
#include <vector>
#include <map>
#include <string>
#include <cmath>
#include <limits>

using namespace std;

const int MAX_MOEDAS = 40;
const double INFINITO = numeric_limits<double>::infinity();
double taxas[MAX_MOEDAS][MAX_MOEDAS];

// Função para detectar arbitragem usando o algoritmo de Floyd-Warshall
bool detectarArbitragem(int numMoedas) {
    // Algoritmo de Floyd-Warshall
    for (int k = 0; k < numMoedas; ++k) {
        for (int i = 0; i < numMoedas; ++i) {
            for (int j = 0; j < numMoedas; ++j) {
                if (taxas[i][k] < INFINITO && taxas[k][j] < INFINITO) {
                    taxas[i][j] = min(taxas[i][j], taxas[i][k] + taxas[k][j]);
                }
            }
        }
    }

    // Verificar se há ciclos negativos
    for (int i = 0; i < numMoedas; ++i) {
        if (taxas[i][i] < 0) {
            return true; // Ciclo negativo detectado
        }
    }

    return false; // Nenhum ciclo negativo encontrado
}

int main() {
    int casoNumero = 1;

    while (true) {
        int numMoedas;
        cin >> numMoedas;
        if (numMoedas == 0) break;

        map<string, int> indiceMoeda;
        for (int i = 0; i < numMoedas; ++i) {
            string nomeMoeda;
            cin >> nomeMoeda;
            indiceMoeda[nomeMoeda] = i;
        }

        // Inicializar a matriz de taxas com infinito
        for (int i = 0; i < numMoedas; ++i) {
            for (int j = 0; j < numMoedas; ++j) {
                taxas[i][j] = (i == j) ? 0.0 : INFINITO;
            }
        }

        int numTaxas;
        cin >> numTaxas;

        for (int i = 0; i < numTaxas; ++i) {
            string moedaOrigem, moedaDestino;
            double taxaCambio;
            cin >> moedaOrigem >> taxaCambio >> moedaDestino;
            int origem = indiceMoeda[moedaOrigem];
            int destino = indiceMoeda[moedaDestino];
            taxas[origem][destino] = -log(taxaCambio);
        }

        bool arbitragem = detectarArbitragem(numMoedas);

        cout << "Caso " << casoNumero++ << ": " << (arbitragem ? "Sim" : "Nao") << endl;
    }

    return 0;
}
```

### Explicação do Código

1. **Inicialização da Matriz `taxas`:**
   - A matriz `taxas` é inicializada com `INFINITO` para representar a ausência de taxas de câmbio entre as moedas.
   - A diagonal principal é inicializada com zero, pois a taxa de câmbio de uma moeda para ela mesma é zero.

2. **Conversão das Taxas de Câmbio:**
