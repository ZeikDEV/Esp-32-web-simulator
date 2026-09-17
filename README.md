# ESP32 Lab

ESP32 Lab é um simulador educacional de microcontroladores que roda diretamente no navegador. A primeira versão entrega um MVP funcional para ESP32 DevKit V1 com GPIO, LED interno, entradas, PWM básico, ADC simulado, editor de código, interpretação controlada e Monitor Serial.

## Recursos

- Seleção entre ESP32 DevKit V1, ESP32-WROOM, ESP32-S3 e ESP32-C3.
- Circuito virtual com representação da placa, LED interno, estados dos GPIOs e monitor de pinos.
- Editor de código com numeração de linhas, exemplos, formatação e mensagens de validação.
- Interpretador seguro para `pinMode`, `digitalWrite`, `digitalRead`, `analogRead`, `analogWrite`, `delay`, `Serial.begin`, `Serial.print` e `Serial.println`.
- Monitor Serial com baud rate simulado, timestamps e limpeza de saída.
- Projetos salvos no `localStorage` do navegador, com criar, salvar, abrir, duplicar, excluir, exportar e importar JSON validado.
- Interface responsiva com navegação por teclado, estados visuais de sucesso/erro e paleta azul-bebê, azul-escuro e branco.

## Segurança

O código inserido pelo usuário **não é executado como JavaScript**. O projeto não usa `eval`, `new Function`, `process.env`, filesystem, shell, comandos do sistema, chamadas de rede ou acesso automático a hardware físico. O conteúdo é analisado por um parser de comandos permitidos e executado em um estado de simulação isolado.

A validação aplica limites de tamanho, linhas, operações, valores de PWM, baud rate e atraso virtual. Funções não suportadas, GPIOs inexistentes, GPIOs somente de entrada e valores fora do intervalo são bloqueados com mensagens amigáveis.

A importação de projetos valida manualmente o formato esperado antes de carregar o conteúdo. A interface renderiza o código como texto em textarea e Monitor Serial como texto React, sem HTML fornecido pelo usuário.

## Stack

- React 19
- TypeScript
- Vite
- Tailwind CSS 4
- Lucide React
- Vercel-compatible static frontend

## Arquitetura

- `client/src/pages/Home.tsx`: layout do laboratório, interação, projetos e estado da UI.
- `client/src/lib/simulator.ts`: definições de placas, parser, validação, limites e executor seguro.
- `client/src/index.css`: tokens visuais e acessibilidade global.
- `client/src/main.tsx`: bootstrap do React.
- `client/src/components/`: componentes compartilhados do template.
- `server/`: runtime estático gerado pelo template; a simulação não depende de backend.

## Como executar localmente

```bash
pnpm install
pnpm dev
```

Abra o endereço exibido pelo Vite. Para checar tipos:

```bash
pnpm check
```

Para gerar a versão de produção:

```bash
pnpm build
```

## Deploy na Vercel

O projeto é um frontend estático compatível com hospedagem padrão da Vercel. Configure o projeto apontando para o diretório raiz, use `pnpm build` como comando de build e publique o conteúdo gerado pelo template. A simulação básica não requer variáveis de ambiente, banco ou processo persistente.

## Como adicionar uma nova placa

Adicione uma entrada em `BOARD_DEFINITIONS` dentro de `client/src/lib/simulator.ts`, informando o identificador, nome, GPIOs, GPIOs somente de entrada, ADCs e LED interno. O parser usa essa definição para validar automaticamente os comandos.

## Como adicionar um novo componente

Crie o tipo do componente junto às definições do simulador e adicione seu estado ao resultado de `runProgram`. Em seguida, conecte a representação visual em `Home.tsx`. O componente deve ser puramente virtual e não pode acessar USB, serial físico, GPIO real ou rede sem uma funcionalidade opt-in separada.

## Como adicionar uma nova função ao interpretador

1. Adicione a função à lista `SUPPORTED_FUNCTIONS`.
2. Crie um tipo de instrução explícito.
3. Adicione a validação de argumentos em `parseProgram`.
4. Adicione a execução controlada em `runProgram`.
5. Defina limites e mensagens de erro específicos.
6. Inclua um exemplo e um teste de aceitação.

Nunca converta o código do usuário em JavaScript executável. Cada operação precisa ter uma regra própria e explícita.

## Limitações conhecidas

O MVP interpreta um subconjunto linear de comandos. Loops, variáveis, expressões arbitrárias, funções definidas pelo usuário e bibliotecas externas são deliberadamente recusados. Os componentes são representações educacionais e não substituem testes com hardware físico. O armazenamento é local ao navegador e não sincroniza entre dispositivos.
