# 🧠 MIPS Visual Simulator - Extensión Instrucciones 32 bits

Este proyecto amplía el simulador visual de MIPS permitiendo una experiencia más completa e intuitiva. Se ha agregado soporte a nuevas instrucciones de 32 bits y mejoras visuales para el seguimiento y comprensión del código MIPS.

---

## 🚀 Cambios Implementados

### ✅ Soporte para nuevas instrucciones de 32 bits

- Se añadieron instrucciones tipo **R**, **I** y **J**.
- Las nuevas instrucciones permiten realizar operaciones como desplazamiento, comparaciones y sumas sin overflow.
- Estas se integraron en la lógica del backend y afectan correctamente la memoria y registros.

### 📚 Instrucciones implementadas

#### 🔷 Tipo R (10)

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

#### 🔷 Tipo I (10)

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

#### 🔷 Tipo J (2)

| Instrucción | Descripción                |
|-------------|----------------------------|
| `j`         | Jump                       |
| `jal`       | Jump and link              |

**🔢 Total: 22 instrucciones de 32 bits**

---

## 👨‍💻 Detalles de implementación

### Backend (Lógica de ejecución)

- En el archivo principal de ejecución (por ejemplo `executeInstruction.js`), se agregaron nuevos `case` en el `switch` para interpretar las nuevas instrucciones.
- Se respetó el formato de MIPS: tipo R usa registros y `shamt`, tipo I usa inmediato, y tipo J solo dirección.
- Para instrucciones como `jal`, se modificó también el `PC` y se guardó el valor de retorno en `$ra`.

### Frontend (Interfaz Visual)

#### Instruction Viewer
- Se implementó un **Instruction Viewer** que permite ver la lista de instrucciones destacando la actual.
- Visualmente es un cuadro similar al debugger y se sincroniza con el `PC`.

#### Memoria y Registros
- Se añadió la posibilidad de contraer y expandir los módulos de Memoria RAM y Registros.
- Se resalta el **registro modificado** durante la ejecución, así como el valor.