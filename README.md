```{r Protocolo, eval=FALSE, include=TRUE}
"Protocolo:

  1. Daniel felipe Villa Rengifo
  
  2. Lenguaje: R

  3. Tema: Manejo de archivos *.txt (realice al menos dos ejercicios que requieran cargar archivos externos, leer y procesar la información del archvo leído, y guardar las respuestas a los ejercicios  en archivos independientes)

  4. Fuentes:
     https://rfaqs.com/reading-text-txt-files/
     https://thepracticalr.wordpress.com/2016/09/23/importing-data-into-r/"
```


# ¿Cómo leer archivos TXT en R?

Puedes leer un archivo TXT en R con la función `read.table`.

## Sintaxis

```{r Sintaxis, eval=FALSE, include=TRUE}
# Sintaxis
read.table(file,                 # Archivo de datos TXT indicado como string o ruta completa al archivo
           header = FALSE,       # Si se muestra el encabezado (TRUE) o no (FALSE)
           sep = "",             # Separador de las columnas del archivo
           dec = ".")            # Caracter utilizado para separar decimales de los números en el archivo
```

> NOTA: La salida de un archivo TXT leído con la función `read.table` será de la clase `data.frame`.

# Leer la base de datos:

Esta base datos contiene los siguientes parametros:

1. Nombre
2. Edad
3. Estado Ciivl
4. Salario Actual de la persona en Millones de pesos
5. Empresa donde labora para recibir el salario.
6. Coche Actual.

Nuestro archivo `Base.txt` contiene 50 datos de personas aleatorias


# 1° Ejercicio:

```{r Leer la base de datos}
# Leer la base de datos:
base1 <- read.table(file = "Base.txt", header = T, sep = "|")
print(base1)

## Convertimos a factor las columnas requeridas:
base1$Estado_Civil <- as.factor(base1$Estado_Civil)

base1$Empresa <- as.factor(base1$Empresa)

base1$Coche_Actual <- as.factor(base1$Coche_Actual)
```

El ejercicio consiste en:

+ ¿Cual es el estado civil y la empresa con mayores ingresos?

```{r}
library(dplyr)

# Seleccionamos las columnas necesarias:
Estado_Ingreso <- select(base1, Estado_Civil, Salario)

print(Estado_Ingreso)

Empresa_Ingreso <- select(base1, Empresa, Salario)

print(Empresa_Ingreso)

## Cremos las respectias tablas:
Salario_Civil <- aggregate(Salario ~ Estado_Civil, Estado_Ingreso, sum)

print(Salario_Civil)

Salario_Empresa <- aggregate(Salario ~ Empresa, Empresa_Ingreso, sum)

print(Salario_Empresa)

## Ahora lo organizamos de menor a mayor:
"Como podemos darnos cuenta, Los solteros generan mayores ingresos los solteros, se puede deducir que las personas solteras se dedican a estudiar por ende no les queda tiempo para hacer o deshacer familias y/o parejas."

Salario_Civil <- arrange(Salario_Civil, Salario)

print(Salario_Civil)

"La empresa con mayores salarios promedios es Borland"

Salario_Empresa <- arrange(Salario_Empresa, Salario)

print(Salario_Empresa)

## Ahora exportamos los resultados

write.table(Salario_Civil, file = "SalarioTOtal_EstadoCivil.txt", row.names = F)

write.table(Salario_Empresa, file = "SalarioTotal_Empresa.txt", row.names = F)
```


+ Los que ganan menos de 1 millon al mes, ¿si vendieran el auto actual, su salario aumentaria para un colchon por un largo tiempo...? 

```{r}
# Precio estandarizado

Precio_Estan <- 50000000

```



```{r}
options(scipen=999)

menor1M <- filter(base1, Salario <= 1000000)

print(menor1M)

menor1M$Salario_Aumentado <- menor1M$Salario * Precio_Estan

"Ahora tendran una buena cantiidad de dinero para "
print(menor1M)
```

```{r}
#Exportamos los resultados:
write.table(menor1M, file = "Salario_Aumentado.txt", row.names = F)
```

# 2° Ejercicio:

En este segundo ejercicio vamos a ver cual es el departamento colombiano _(Solamente contiene 14 departamentos)_ con mayores fumadores.

```{r}
# Leer la base de datos:
base2 <- read.table(file = "Base2.txt", header = T, sep = "|")
print(base2)


# Convritamos en factor dos columnas:
base2$Fuma <- as.factor(base2$Fuma)
print(base2$Fuma)

base2$Departamento <- as.factor(base2$Departamento)
print(base2$Departamento)
```

```{r}
# Ahora fabrico una tabla donde Nos muestre la tabla pedida:

fumaXdepartamento <- with(base2, table(Departamento, Fuma))

# Convertimos en data frame la tabla anterior:

fumaXdepartamento <- data.frame(fumaXdepartamento)

# Filtramos la tabla por fumadores (Yes)

FumaYes <- filter(fumaXdepartamento, Fuma == "Yes")

"Observemos que el departamento con mayor fumadores es Antioquia"

# Ahora exportemos la tabla:

write.table(FumaYes, file = "FumadoresActivosXDepartamento.txt", row.names = F)
```