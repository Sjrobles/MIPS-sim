#  MIPS Visual Simulator - Extensión Instrucciones 32 bits

Este proyecto amplía el simulador visual de MIPS permitiendo una experiencia más completa e intuitiva. Se ha agregado soporte a nuevas instrucciones de 32 bits y mejoras visuales para el seguimiento y comprensión del código MIPS.

---

##  Cambios Implementados

###  Soporte para nuevas instrucciones de 32 bits

- Se añadieron instrucciones tipo **R**, **I** y **J**.
- Las nuevas instrucciones permiten realizar operaciones como desplazamiento, comparaciones y sumas sin overflow.
- Estas se integraron en la lógica del backend y afectan correctamente la memoria y registros.

###  Instrucciones implementadas

####  Tipo R (10)

| Instrucción | Descripción                       |
|-------------|-----------------------------------|
| `add`       | Suma con overflow                 |
| `sub`       | Resta con overflow                |
| `and`       | AND lógico                        |
| `or`        | OR lógico                         |
| `slt`       | Set on less than (signed)         |
| `addu`      | Suma sin overflow (unsigned)      |
| `subu`      | Resta sin overflow (unsigned)     |
| `sltu`      | Set on less than (unsigned)       |
| `sll`       | Shift left logical                |
| `srl`       | Shift right logical               |

####  Tipo I (10)

| Instrucción | Descripción                                  |
|-------------|----------------------------------------------|
| `addi`      | Suma inmediata con overflow                  |
| `andi`      | AND inmediato                                |
| `ori`       | OR inmediato                                 |
| `lw`        | Load word desde memoria                      |
| `sw`        | Store word en memoria                        |
| `beq`       | Branch if equal                              |
| `bne`       | Branch if not equal                          |
| `addiu`     | Suma inmediata sin overflow (unsigned)       |
| `slti`      | Set less than immediate (signed)             |
| `sltiu`     | Set less than immediate unsigned             |

####  Tipo J (2)

| Instrucción | Descripción                |
|-------------|----------------------------|
| `j`         | Jump                       |
| `jal`       | Jump and link              |

** Total: 22 instrucciones de 32 bits**

---

##  Detalles de implementación

##  Diccionario de Instrucciones (`instructionSet`)

El archivo contiene un diccionario organizado por tipo de instrucción (R, I, J) y define los códigos `opcode` y `funct` utilizados durante la codificación de instrucciones:

```js
const instructionSet = {
  R: {
    "add":  { funct: "100000" },
    "addu": { funct: "100001" },
    "sub":  { funct: "100010" },
    "subu": { funct: "100011" },
    "and":  { funct: "100100" },
    "or":   { funct: "100101" },
    "slt":  { funct: "101010" },
    "sltu": { funct: "101011" }
  },
  I: {
    "addi":  { opcode: "001000" },
    "addiu": { opcode: "001001" },
    "slti":  { opcode: "001010" },
    "sltiu": { opcode: "001011" },
    "andi":  { opcode: "001100" },
    "ori":   { opcode: "001101" },
    "lw":    { opcode: "100011" },
    "sw":    { opcode: "101011" },
    "beq":   { opcode: "000100" },
    "bne":   { opcode: "000101" }
  },
  J: {
    "j":    { opcode: "000010" },
    "jal":  { opcode: "000011" }
  }
};
```

Esto permite generar automáticamente la codificación hexadecimal de las instrucciones según sus componentes (`opcode`, `rs`, `rt`, `rd`, `shamt`, `funct`, `immediate`, `address`, etc.)

---

##  Ejecución de Instrucciones (`executeMIPSInstruction`)

El núcleo funcional de la simulación es la función `executeMIPSInstruction`, que interpreta una instrucción en formato ensamblador, ejecuta la lógica correspondiente sobre los registros o memoria, y retorna el nuevo valor del PC (Program Counter). Esto permite simular el comportamiento real del procesador MIPS de forma intuitiva.

Este es un extracto de la función para entender su estructura y flujo:

```js
function executeMIPSInstruction(instruction, registers, memory, PC) {
  const [op, ...operands] = instruction.trim().split(/\s+/);

  switch (op) {
    case "add": {
      const [rd, rs, rt] = operands;
      registers[rd] = registers[rs] + registers[rt];
      break;
    }
    case "addu": {
      const [rd, rs, rt] = operands;
      registers[rd] = (registers[rs] >>> 0) + (registers[rt] >>> 0);
      break;
    }
    case "sub": {
      const [rd, rs, rt] = operands;
      registers[rd] = registers[rs] - registers[rt];
      break;
    }
    case "subu": {
      const [rd, rs, rt] = operands;
      registers[rd] = (registers[rs] >>> 0) - (registers[rt] >>> 0);
      break;
    }
    case "slt": {
      const [rd, rs, rt] = operands;
      registers[rd] = registers[rs] < registers[rt] ? 1 : 0;
      break;
    }
    case "sltu": {
      const [rd, rs, rt] = operands;
      registers[rd] = (registers[rs] >>> 0) < (registers[rt] >>> 0) ? 1 : 0;
      break;
    }
    case "and": {
      const [rd, rs, rt] = operands;
      registers[rd] = registers[rs] & registers[rt];
      break;
    }
    case "or": {
      const [rd, rs, rt] = operands;
      registers[rd] = registers[rs] | registers[rt];
      break;
    }
    case "sll": {
      const [rd, rt, shamt] = operands;
      registers[rd] = registers[rt] << parseInt(shamt, 0);
      break;
    }
    case "srl": {
      const [rd, rt, shamt] = operands;
      registers[rd] = registers[rt] >>> parseInt(shamt, 0);
      break;
    }
    case "addi": {
      const [rt, rs, immediate] = operands;
      registers[rt] = registers[rs] + parseInt(immediate, 0);
      break;
    }
    case "addiu": {
      const [rt, rs, immediate] = operands;
      registers[rt] = (registers[rs] >>> 0) + (parseInt(immediate, 0) >>> 0);
      break;
    }
    case "andi": {
      const [rt, rs, immediate] = operands;
      registers[rt] = registers[rs] & parseInt(immediate, 0);
      break;
    }
    case "ori": {
      const [rt, rs, immediate] = operands;
      registers[rt] = registers[rs] | parseInt(immediate, 0);
      break;
    }
    case "slti": {
      const [rt, rs, immediate] = operands;
      registers[rt] = registers[rs] < parseInt(immediate, 0) ? 1 : 0;
      break;
    }
    case "sltiu": {
      const [rt, rs, immediate] = operands;
      registers[rt] = (registers[rs] >>> 0) < (parseInt(immediate, 0) >>> 0) ? 1 : 0;
      break;
    }
    case "lw": {
      const [rt, rs, offset] = operands;
      const address = registers[rs] + parseInt(offset, 0);
      if (memory.hasOwnProperty(address)) {
        registers[rt] = memory[address];
      } else {
        console.error("Memory address not found:", address);
      }
      break;
    }
    case "sw": {
      const [rt, rs, offset] = operands;
      const address = registers[rs] + parseInt(offset, 0);
      memory[address] = registers[rt];
      break;
    }
    case "beq": {
      const [rs, rt, offset] = operands;
      if (registers[rs] === registers[rt]) {
        return PC + parseInt(offset, 0);
      }
      break;
    }
    case "bne": {
      const [rs, rt, offset] = operands;
      if (registers[rs] !== registers[rt]) {
        return PC + parseInt(offset, 0);
      }
      break;
    }
    case "j": {
      const [address] = operands;
      return parseInt(address, 0);
    }
    case "jal": {
      const [address] = operands;
      registers["ra"] = PC + 1;
      return parseInt(address, 0);
    }
    default: {
      console.error("Unsupported operation:", op);
      break;
    }
  }

  return PC + 1;
}
```

Esta lógica modular permite expandir el simulador fácilmente con nuevas instrucciones en el futuro.

En este fragmento, se implementaron las nuevas funciones de 32 bits para ser funcionales y poder ser interpretadas, asi mismo, que dichas instrucciones tengan repercusiones sobre la memoria y los registros del simulador. 


###  Instruction Viewer

Se implementó un componente visual llamado **Instruction Viewer**, el cual se muestra **junto al Debugger** y permite observar de forma clara y ordenada cuál instrucción se está ejecutando en cada ciclo del programa. Esto mejora significativamente la experiencia del usuario al simular código MIPS, permitiendo visualizar el flujo de ejecución paso a paso.

####  Estética y diseño

- Presenta un diseño en forma de tarjeta similar al Debugger.
- Aparece en paralelo al Debugger dentro del entorno visual.
- Utiliza sombreado y bordes suaves para mejorar la lectura.
- Resalta la instrucción actual basada en el valor del contador de programa (`PC`) con un fondo color crema y texto en negrita.

####  Fragmento de código JSX

```jsx
{/* Lista de instrucciones en un estilo similar al Debugger */}
<div
  className="instruction-list"
  style={{
    backgroundColor: "#f8f9fa",
    border: "1px solid #dee2e6",
    borderRadius: "8px",
    padding: "1rem",
    marginTop: "1rem",
    maxHeight: "200px",
    overflowY: "auto",
    boxShadow: "0 2px 6px rgba(0, 0, 0, 0.1)",
    width: "100%",
  }}
>
  <h4 style={{ marginBottom: "0.5rem", fontSize: "1rem", fontWeight: "bold" }}>
    Instruction Viewer
  </h4>
  {mipsInput.trim().split('\n').map((inst, index) => (
    <pre
      key={index}
      style={{
        backgroundColor: index === PC ? '#ffefc1' : 'transparent',
        fontWeight: index === PC ? 'bold' : 'normal',
        padding: '4px 8px',
        borderRadius: '4px',
        margin: 0,
      }}
    >
      {inst}
    </pre>
  ))}
</div>
```
#  Detección de Overflow en Instrucciones MIPS

##  ¿Qué es esta sección visual?

Para mejorar la **interactividad y depuración visual** del simulador MIPS, se añadió una sección que muestra si la **última instrucción ejecutada generó un overflow aritmético**.

Se representa con el siguiente componente visual en React:

```jsx
<div style={{
  backgroundColor: registers._overflow ? '#ff4d4f' : '#e0e0e0',
  color: registers._overflow ? 'white' : 'black',
  padding: '10px',
  margin: '1rem 0',
  borderRadius: '8px',
  textAlign: 'center',
  fontWeight: 'bold',
  boxShadow: '0 2px 8px rgba(0,0,0,0.15)'
}}>
  {registers._overflow
    ? '⚠️ ¡Overflow detectado en la última instrucción!'
    : '✅ Sin overflow detectado'}
</div>
```
## 🧠 ¿Por qué se agregó?

El overflow aritmético ocurre cuando el resultado de una operación **signed** (con signo) excede los límites de un número entero de 32 bits (−2³¹ a 2³¹−1).

Este indicador se agregó para:

- ✅ Mostrar al usuario cuándo ocurre un **overflow**
- 🐞 Facilitar la **depuración** de instrucciones
- 🧪 Ser un recurso **educativo visual** para comprender cómo MIPS maneja operaciones signed

---

## ⚙️ ¿Qué instrucciones lo utilizan?

El campo `registers._overflow` solo es actualizado por instrucciones que requieren **detección explícita de overflow**:

| Instrucción | Tipo | Detecta Overflow |
|-------------|------|------------------|
| `add`       | R    | ✅ Sí             |
| `sub`       | R    | ✅ Sí             |
| `addi`      | I    | ✅ Sí             |

Otras instrucciones como `addu`, `subu`, `addiu` **no lo detectan** porque son **unsigned** y no lanzan excepciones por overflow.


