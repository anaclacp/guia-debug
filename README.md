# Debugging Guide 🔍
> Guia completo para identificação e resolução de problemas no código

## Sumário
1. [Fundamentos do Debugging](#fundamentos-do-debugging)
2. [Técnicas de Debugging](#técnicas-de-debugging)
3. [Ferramentas](#ferramentas)
4. [Console Debugging](#console-debugging)
5. [IDE Debugging](#ide-debugging)
6. [Debugging em Produção](#debugging-em-produção)
7. [Performance Debugging](#performance-debugging)
8. [Melhores Práticas](#melhores-práticas)

## Fundamentos do Debugging

### Processo Sistemático
1. Reproduzir o problema
2. Isolar o contexto
3. Identificar o ponto de falha
4. Analisar o estado do sistema
5. Corrigir o problema
6. Verificar a solução
7. Prevenir recorrências

### Tipos de Bugs Comuns
- Erros de Sintaxe
- Erros de Lógica
- Erros de Runtime
- Memory Leaks
- Race Conditions
- Edge Cases
- Problemas de Performance

## Técnicas de Debugging

### Debugging Científico
```javascript
// 1. Hipótese
// "O cálculo de desconto está errado para valores acima de 1000"

// 2. Experimento
function testarCalculoDesconto() {
    console.log('Teste 1:', calcularDesconto(500));  // Caso base
    console.log('Teste 2:', calcularDesconto(1000)); // Limite
    console.log('Teste 3:', calcularDesconto(1001)); // Problema?
}

// 3. Análise
// Comparar resultados esperados vs. obtidos

// 4. Conclusão
// Identificar padrão e corrigir
```

### Binary Search Debugging
```javascript
// Identificar problema em arquivo grande
let inicio = 0;
let fim = codigo.length;

while (inicio <= fim) {
    let meio = Math.floor((inicio + fim) / 2);
    if (testarCodigo(codigo.slice(0, meio))) {
        // Problema está na segunda metade
        inicio = meio + 1;
    } else {
        // Problema está na primeira metade
        fim = meio - 1;
    }
}
```

## Ferramentas

### Browser DevTools
```javascript
// Chrome DevTools - Exemplos básicos

// 1. Console API avançada
console.log('Dado simples');
console.table([{id: 1, nome: 'Item 1'}, {id: 2, nome: 'Item 2'}]);
console.group('Grupo de Logs');
console.log('Detalhes...');
console.groupEnd();

// 2. Debugging condicional
let contador = 0;
function processo() {
    contador++;
    if (contador === 5) {
        debugger; // Pausa execução
    }
    // resto do código
}

// 3. Network monitoring
fetch('/api/dados')
    .then(response => {
        console.log('Response Headers:', response.headers);
        return response.json();
    })
    .then(data => console.log('Dados:', data));
```

## Console Debugging

### Técnicas Avançadas
```javascript
// 1. Logging estruturado
const log = {
    info: (msg, data) => console.log(`[INFO] ${msg}`, data),
    error: (msg, err) => console.error(`[ERROR] ${msg}`, err),
    debug: (msg, data) => console.debug(`[DEBUG] ${msg}`, data),
    trace: (msg) => console.trace(`[TRACE] ${msg}`)
};

// 2. Performance logging
console.time('operacao');
// código a ser medido
console.timeEnd('operacao');

// 3. Stack trace customizado
function gerarStackTrace() {
    try {
        throw new Error('Stack trace');
    } catch (e) {
        return e.stack;
    }
}

// 4. Console estilizado
console.log(
    '%c Debug Info ',
    'background: #222; color: #bada55',
    'Dados importantes'
);
```

## IDE Debugging

### VSCode Debug Configurations
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "Debug Atual",
            "program": "${file}",
            "skipFiles": [
                "<node_internals>/**"
            ]
        },
        {
            "type": "chrome",
            "request": "launch",
            "name": "Debug Chrome",
            "url": "http://localhost:3000",
            "webRoot": "${workspaceFolder}"
        }
    ]
}
```

### Breakpoints Avançados
```javascript
// 1. Breakpoint condicional
let items = [];
for (let i = 0; i < 1000; i++) {
    items.push({ id: i, valor: Math.random() });
    // Breakpoint quando valor > 0.95
}

// 2. Logpoint
function processarDados(dados) {
    // Log sem parar execução
    console.log(`Processando: ${dados.length} items`);
}

// 3. Watch expressions
let contador = 0;
let soma = 0;
// Watch: contador + soma
// Watch: soma / contador
```

## Debugging em Produção

### Error Handling
```javascript
// 1. Try-Catch estruturado
async function operacaoSegura() {
    try {
        await operacaoArriscada();
    } catch (error) {
        logger.error({
            message: error.message,
            stack: error.stack,
            timestamp: new Date(),
            context: {
                user: getCurrentUser(),
                action: 'operacaoArriscada'
            }
        });
    }
}

// 2. Error Boundaries (React)
class ErrorBoundary extends React.Component {
    state = { hasError: false, error: null };
    
    static getDerivedStateFromError(error) {
        return { hasError: true, error };
    }
    
    componentDidCatch(error, errorInfo) {
        logErrorToService(error, errorInfo);
    }
    
    render() {
        if (this.state.hasError) {
            return <FallbackComponent error={this.state.error} />;
        }
        return this.props.children;
    }
}
```

### Logging em Produção
```javascript
// 1. Logger configurável
const logger = {
    level: process.env.LOG_LEVEL || 'info',
    
    info: (msg) => {
        if (logger.shouldLog('info')) {
            logger.log('INFO', msg);
        }
    },
    
    error: (msg, error) => {
        if (logger.shouldLog('error')) {
            logger.log('ERROR', msg, error);
        }
    },
    
    shouldLog: (level) => {
        const levels = ['debug', 'info', 'warn', 'error'];
        return levels.indexOf(level) >= levels.indexOf(logger.level);
    },
    
    log: (level, msg, error = null) => {
        const logData = {
            timestamp: new Date().toISOString(),
            level,
            message: msg,
            error: error ? {
                message: error.message,
                stack: error.stack
            } : null
        };
        
        // Enviar para serviço de logging
        console.log(JSON.stringify(logData));
    }
};
```

## Performance Debugging

### Identificação de Gargalos
```javascript
// 1. Performance API
const perfMarks = {
    start: (label) => performance.mark(`${label}-start`),
    end: (label) => {
        performance.mark(`${label}-end`);
        performance.measure(label, 
            `${label}-start`, 
            `${label}-end`);
    },
    get: (label) => {
        const measures = performance.getEntriesByName(label);
        return measures[measures.length - 1];
    }
};

// 2. Memory leaks
function detectarMemoryLeak() {
    let arr = [];
    setInterval(() => {
        arr.push(new Array(1000000));
        console.log(process.memoryUsage());
    }, 1000);
}

// 3. CPU Profiling
console.profile('operacaoPesada');
// código a ser analisado
console.profileEnd('operacaoPesada');
```

## Melhores Práticas

### Prevenção de Bugs
```javascript
// 1. Validações defensivas
function processarDados(dados) {
    if (!Array.isArray(dados)) {
        throw new TypeError('Dados devem ser um array');
    }
    
    if (dados.length === 0) {
        return {
            status: 'empty',
            resultado: null
        };
    }
    
    // processamento
}

// 2. Tipos fortes (TypeScript)
interface Usuario {
    id: number;
    nome: string;
    email: string;
}

function validarUsuario(usuario: Usuario): boolean {
    return Boolean(
        usuario.id &&
        usuario.nome &&
        usuario.email.includes('@')
    );
}

// 3. Design by Contract
function dividir(a: number, b: number): number {
    // Pré-condições
    if (typeof a !== 'number' || typeof b !== 'number') {
        throw new TypeError('Argumentos devem ser números');
    }
    if (b === 0) {
        throw new Error('Divisão por zero');
    }
    
    // Operação
    const resultado = a / b;
    
    // Pós-condições
    if (isNaN(resultado)) {
        throw new Error('Resultado inválido');
    }
    
    return resultado;
}
```

### Debug Checklist
1. Reproduziu o bug consistentemente?
2. Isolou o contexto mínimo?
3. Verificou logs relevantes?
4. Testou em diferentes ambientes?
5. Documentou a solução?
6. Adicionou testes para prevenir regressão?
7. Revisou mudanças relacionadas recentes?

## Recursos Adicionais
- [Chrome DevTools Documentation](https://developers.google.com/web/tools/chrome-devtools)
- [VSCode Debugging](https://code.visualstudio.com/docs/editor/debugging)
- [Node.js Debugging Guide](https://nodejs.org/en/docs/guides/debugging-getting-started)
- [React DevTools](https://reactjs.org/blog/2019/08/15/new-react-devtools.html)
