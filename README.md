
Engenharia de Software II
Categoria: Comportamental
## Trabalho – Padrões de Desenvolvimento - Command / Observer / Template Method
Integrantes: [Luís Matheus, Vicente Rochefort e João Vítor Goes]


---

## Objetivo do trabalho

Estudar e demonstrar 3 padrões comportamentais do GoF — **Command**, **Observer** e **Template_method** — com explicações, exemplos em **TypeScript** mostrando *sem padrão* e *com padrão*, análise de pontos fortes/fracos, comparativo entre os padrões.

---

##  Estrutura do repositório

```
padroes-de-desenvolvimento/
├── README.md
├── command/
│   ├── sem-padrao/
│   │   └── index.ts
│   └── com-padrao/
│       └── index.ts
├── observer/
│   ├── sem-padrao/
│   │   └── index.ts
│   └── com-padrao/
│       └── index.ts
├── template_method/
│   ├── sem-padrao/
│   │   └── index.ts
│   └── com-padrao/
│       └── index.ts
```


# 1. COMMAND

##  Descrição do Padrão Command

O padrão *Command* tem como propósito encapsular uma requisição (ação) em um objeto. Dessa forma, comportamentos podem ser parametrizados, armazenados, enfileirados, desfeitos ou executados posteriormente, separando quem solicita a ação de quem executa.

Ele é útil em cenários onde há necessidade de criar sistemas com histórico de ações, mecanismos de desfazer/refazer, filas de comandos, macros ou quando se deseja reduzir o acoplamento entre a interface e a lógica de execução.

---

## Objetivo

- Encapsular solicitações em objetos.
- Desacoplar o emissor (invoker) do receptor (receiver).
- Permitir armazenar, enfileirar, registrar e desfazer comandos.

---

## Estrutura

1. **Command**: interface que declara o método `execute()`.
2. **ConcreteCommand**: implementação concreta da ação.
3. **Invoker**: recebe o comando e o aciona.
4. **Receiver** (opcional): objeto que realiza o trabalho interno.
5. **Client**: configura tudo e escolhe qual comando utilizar.

---

# Problema: Código sem o Padrão

A seguir, um exemplo simples onde um controle remoto executa ações de forma direta, através de condicionais. Esse formato dificulta a escalabilidade e aumenta o acoplamento:

```ts
// command/sem-padrao/index.ts

class ControleRemoto {
  ligarLuz() {
    console.log("Luz ligada");
  }

  desligarLuz() {
    console.log("Luz desligada");
  }
}

const controle = new ControleRemoto();
controle.ligarLuz();
controle.desligarLuz();
```

Nesse modelo, adicionar novas ações exige modificar diretamente o ControleRemoto, tornando o código rígido e pouco extensível.


# Solução com o Padrão Command

**Interface Command**
```ts
interface Command {
  execute(): void;
}

// Comandos Concretos
class LigarLuz implements Command {
  execute() {
    console.log("Lâmpada ligada");
  }
}

class DesligarLuz implements Command {
  execute() {
    console.log("Lâmpada desligada");
  }
}
 // Invoker
  Invocador (Controle Remoto)
class ControleRemoto {
  executar(comando: Command) {
    comando.execute();
  }
}

 // Uso do Padrão
const controle = new ControleRemoto();

controle.executar(new LigarLuz());
controle.executar(new DesligarLuz());
```



## Estrutura do Padrão (visão geral)

*Command:* Interface que define a operação a ser executada.

*ConcreteCommand:* Implementações específicas das ações.

*Invoker:* Objeto que solicita a execução dos comandos.

*Receiver (opcional):* Objeto que realiza o trabalho real.

*Client:* Configura e associa comandos ao invocador.

## Pontos Fortes

 - Reduz o acoplamento entre quem solicita e quem executa a ação.

 - Facilita a adição de novos comandos sem alterar o código existente.

 - Permite armazenar comandos em listas, filas ou pilhas.

 - Possibilita a implementação de funcionalidades como desfazer/refazer.

 - Organização clara do código, facilitando manutenção e testes.

## Pontos Fracos

 - Pode gerar grande número de classes em sistemas complexos.

 - Para comandos muito simples, pode parecer código excessivo.

 - Estrutura ligeiramente mais complexa quando comparada a chamadas diretas.

# Conclusão

O padrão **Command** oferece uma forma robusta e organizada de encapsular ações, tornando sistemas mais flexíveis, extensíveis e de baixo acoplamento. Sua aplicação é especialmente útil em contextos que requerem histórico de operações, automação de tarefas, controles remotos, filas de execução e ações configuráveis.

Este estudo demonstrou a diferença entre uma implementação direta e uma estrutura baseada no padrão, evidenciando ganhos consideráveis na modularidade e manutenção.

# 3. TEMPLATE METHOD
## Descrição do Padrão Template Method

O padrão Template Method define o esqueleto de um algoritmo dentro de um método, deixando para as subclasses a responsabilidade de implementar partes específicas desse processo.
Ou seja: ele fixa a estrutura geral e permite que apenas passos específicos variem.

Esse padrão é muito útil quando:

Várias classes possuem a mesma sequência de etapas, mas com partes diferentes;

Quando queremos evitar duplicação de lógica;

Quando é necessário impor uma ordem fixa no algoritmo, mas ainda assim permitir extensões.

## Objetivo

Definir o esqueleto de um algoritmo dentro de um método final (template).

Permitir que subclasses modifiquem apenas partes específicas do processo.

Evitar duplicação de código.

Garantir que a ordem das operações seja sempre respeitada.

## Estrutura

Template (classe abstrata)
Define o método principal (template) que organiza os passos.

Primitive Operations (métodos abstratos)
Etapas que devem ser implementadas por subclasses.

Hook Methods (opcional)
Etapas padrão que podem ser sobrescritas.

Concrete Classes
Implementam as variações do algoritmo.

## Problema: Código sem o Padrão

No exemplo a seguir, temos dois tipos de relatórios sendo gerados. Ambos seguem quase o mesmo processo, porém o código é duplicado e difícil de manter:

// template_method/sem-padrao/index.ts
```ts
// template_method/sem-padrao/index.ts

class ProcessarPedidoOnline {
  processar() {
    console.log("Validando pagamento do pedido online...");
    console.log("Separando itens no estoque...");
    console.log("Enviando pedido pelos Correios...");
    console.log("Pedido online finalizado!");
  }
}

class ProcessarPedidoPresencial {
  processar() {
    console.log("Registrando pagamento no caixa...");
    console.log("Entregando produto ao cliente...");
    console.log("Pedido presencial finalizado!");
  }
}

const pedidoOnline = new ProcessarPedidoOnline();
pedidoOnline.processar();

const pedidoPresencial = new ProcessarPedidoPresencial();
pedidoPresencial.processar();
```

## Problemas desse modelo:

Várias classes repetem a mesma lógica (“Coletando dados…”).

Se quisermos mudar um passo, precisamos alterar todas as classes manualmente.

O algoritmo não tem uma ordem garantida — cada classe pode fazer diferente sem querer.

Solução com o Padrão Template Method

Criamos uma classe abstrata que define o esqueleto do algoritmo:

// template_method/com-padrao/index.ts

```ts
// Classe abstrata que define o esqueleto do algoritmo
abstract class ProcessarPedidoTemplate {
  processar() {
    this.validarPagamento();
    this.entregarPedido();
    this.finalizar();
  }

  abstract validarPagamento(): void;
  abstract entregarPedido(): void;

  private finalizar() {
    console.log("Pedido finalizado!\n");
  }
}

// Pedido Online
class ProcessarPedidoOnline extends ProcessarPedidoTemplate {
  validarPagamento() {
    console.log("Validando pagamento do pedido online...");
  }

  entregarPedido() {
    console.log("Enviando pedido pelos Correios...");
  }
}

// Pedido Presencial
class ProcessarPedidoPresencial extends ProcessarPedidoTemplate {
  validarPagamento() {
    console.log("Registrando pagamento no caixa...");
  }

  entregarPedido() {
    console.log("Entregando produto ao cliente...");
  }
}

const online = new ProcessarPedidoOnline();
online.processar();

const presencial = new ProcessarPedidoPresencial();
presencial.processar();
```

## Estrutura do Padrão (Visão Geral)

# Template Method
Método que define a sequência do algoritmo.

# Primitive Operations
Passos obrigatórios e abstratos a serem implementados.

# Concrete Classes
Fornecem as variações necessárias.

# Hooks (opcional)
Etapas com comportamento padrão, podendo ou não ser sobrescritas.

## Pontos Fortes

Remove duplicação de código.

Garante que o algoritmo siga sempre a mesma ordem.

Facilmente extensível (basta criar novas subclasses).

Facilita manutenção: mudanças no fluxo geral precisam ser feitas apenas na classe abstrata.

## Pontos Fracos

Pode aumentar o acoplamento entre superclasse e subclasses.

Subclasses precisam conhecer bem o algoritmo para implementar corretamente.

Pode “engessar” o fluxo se o template for muito rígido.

# Conclusão

O padrão Template Method é ideal quando múltiplas classes seguem um fluxo semelhante, mas com pequenas variações. Ele centraliza o algoritmo e reduz significativamente duplicações, oferecendo uma forma limpa e extensível de padronizar processos.
