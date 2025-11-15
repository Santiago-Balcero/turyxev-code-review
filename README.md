# Revisión de código
Por: [Santiago Balcero](https://github.com/Santiago-Balcero) 

#### Proyecto analizado: [TuryxEv_Back](https://github.com/DianaArevalo/TuryxEv_Back)

## Configuración del proyecto y creación de ambiente local de desarrollo ⚠️
- El archivo `README.me` no proporciona información importante para la creación de un ambiente local de desarrollo que permita a nuevas personas integrarse al equipo de trabajo. Un `README.me` de proyecto debe contener el paso a paso para descargar el repositorio, instalar dependencias, montar la o las bases de datos locales (script .sh, script .sql o archivo docker compose), ejecutar los tests y ejecutar el proyecto. Como sugerencia el `README.me` también puede incluir un listado de los endpoints principales para probar en local (curls, colección postman o mínimo ejemplos de los requests y archivos JSON esperados). Tener en cuenta que los usuarios del `README.me` no son los usuarios de la aplicación sino el equipo de desarrollo. Lectura recomendada [aquí](https://medium.com/@fulton_shaun/readme-rules-structure-style-and-pro-tips-faea5eb5d252).
- Al ser un proyecto de naturaleza compleja recomiendo seguir la especificación [OpenAPI](https://swagger.io/specification/) como parte de la documentación de los endpoints, métodos y códigos http, parámetros de las URL, objetos esperados en los cuerpos de la petición y objetos esperados como respuesta. Ejemplo [aquí](https://petstore.swagger.io/).
- Al descargar el repositorio, estar parado sobre la rama `develop` el `11 de Noviembre de 2025 a las 7:30am` y ejecutar los tests con `npm run test` se encuentra que hay varios tests que no funcionan. Recomiendo incorporar un pipeline con GitHub actions que valide la ejecución de los tests al abrir un Pull Request a la rama `develop` y a la rama `main`. ![Resultado de ejecución de tests](images/image.png)
- Al descargar el repositorio, estar parado sobre la rama `develop` el `11 de Noviembre de 2025 a las 7:30am` y ejecutar el proyecto con `npm run dev` se encuentra que el proyecto no compila debido al error en la imagen. Recomiendo incorporar un pipeline con GitHub actions que valide que el proyecto compila y se inicia correctamente al abrir un Pull Request a la rama `develop` y a la rama `main`. ![Error módulo no encontrado](images/image-1.png)
- Al arreglar el error el proyecto sigue sin compilar debido a que falla la conexión con MongoDB. ![Corrección error módulo no encontrado](/images/image-2.png)
- Recomiendo que la rama develop esté siempre estable: tests se ejecutan correctamente, el proyecto compila y se inicia el servidor.

## Elección de base de datos ⚠️
- El proyecto actualmente se conecta a una base de datos no relacional (Mongo DB). Esta puede no ser la mejor elección para un proyecto con las características de este. Un sistema de reservas y pagos tiene procesos que ejecutan mútiples lecturas, inserciones y actualizaciones de registros que necesitan de las características ACID (atomicidad, consistencia, aislamiento y durabilidad) que tienen larga trayectoria en las bases de datos relacionales. Referencias sobre ACID [aquí](https://medium.com/@artemkhrenov/acid-properties-explained-building-reliable-database-transactions-08fdeb9d3153) y [aquí](https://www.datacamp.com/blog/acid-transactions). Recomiendo evaluar la posibilidad de mirgrar a un motor de base de datos relacional.
- En el ecosistema fintech, bancario tradicional y de pagos el estándar es manejar los modelos de datos del core con bases de datos relacionales por sus características ACID. Lecturas recomendadas: [paper académico](https://www.academia.edu/126688677/SQL_vs_NoSQL_Databases_Choosing_the_Right_Option_for_FinTech#:~:text=SQL%20databases%20are%20very%20suitable,and%20strategies%20for%20future%20growth.), [artículo en Medium](https://medium.com/@keemsisi/the-ideal-database-for-financial-transactions-unraveling-the-best-options-d5fef359fe09). En caso de requerirse puede adoptarse una arquitectura en la que el core de procesamiento de pagos y reservas se maneje con una base de datos relacional y otras características como comentarios, reseñas y mensajes entre usuarios se manejen con una base de datos no relacional.
- Recomiendo añadir un archivo docker compose al repositorio para montar de manera fácil la base de datos en local a través de un contenedor dado que es la manera más ágil y rápida de hacerla disponible para el desarrollo. Referencias y ejemplos [aquí](https://medium.com/@akshatgadodia/simplify-database-management-with-docker-compose-a-comprehensive-guide-f33816ff4bc3).

## Observabilidad ❌
- El proyecto no cuenta en absoluto con un sistema de logging, métricas o trazas. Esto hace que al poner el proyecto en producción o incluso en un ambiente de pruebas no haya manera alguna de saber qué está pasando con la aplicación en tiempo real. Los logs ayudan a entender el comportamiento, detectar errores y hacer auditorías. El ciclo de vida del desarrollo de software incluye el monitoreo y la observabilidad como herramientas claves para el mantenimiento y futuras iteraciones de los sitemas. Recomiendo dar prioridad a este ítem y no poner el proyecto en ambiente productivo hasta tanto no se tenga un mínimo de observabilidad. Recomiendo manejar logs estructurados por request. Lectura recomendada sobre logs estructurados [aquí](https://stripe.com/blog/canonical-log-lines).

## Estilo de código y linter ❌
El repositorio carece de dos configuraciones importantes para ayudar a mantener la calidad del código y un estilo consistente que facilite su lectura sin importar qué tan grande sea el equipo de trabajo. `ESLint` es un linter que ayuda a mantener buenas prácticas y a detectar problemas con la calidad del código, documentación [aquí](https://eslint.org/). `Prettier` es una herramienta para el formateo automático del código para mantener un estilo consistente en los archivos, documentación [aquí](https://prettier.io/docs/). Ambas son solo dos opciones entre muchas y requieren sencillos archivos de configuración. Recomiendo su uso dado que la revisión general de los archivos revela un estilo inconsistente. También recomiendo la instalación y uso de las extensiones de VS Code/Cursor `Prettier - Code formatter` (extensión oficial de Prettier) y `ESLint` (extensión oficial de Microsoft).

## Configuración de TypeScript ⚠️

El archivo `tsconfig.json` tiene varias configuraciones comentadas que podrían mejorar significativamente la calidad del código y detectar errores tempranamente:

**Configuraciones recomendadas para habilitar:**
- `"noUnusedLocals": true` - Detecta variables locales no utilizadas.
- `"noUnusedParameters": true` - Detecta parámetros de función no utilizados.
- `"noImplicitReturns": true` - Requiere return explícito en todas las ramas.
- `"exactOptionalPropertyTypes": true` - Manejo más estricto de propiedades opcionales.
- `"noUncheckedIndexedAccess": true` - Añade 'undefined' a tipos accedidos por índice.

**Problemas identificados:**
- La inclusión `"Hotel/**/*"` es inconsistente con la estructura del proyecto (`/src/lib/Hotel/`).
- Debería ser solo `"src/**/*"` para mantener consistencia.

## Archivo configEjemplo.ts ❌
El nombre del archivo es confuso. ¿Por qué `...Ejemplo`?  

```typescript
// Estos valores deberían hacer parte del .env y ser leídos desde allí.
export const config = { // Este objeto de configuración debería estar tipado.
  mongoUri: "MONGO_URI_HERE", // Valor quemado, mala práctica
  port: 3000 // Valor quemado, mala práctica
};
```

Versión mejorada:  
```typescript
// ----- Archivo main.ts
import { loadEnvFile } from "process";

// Antes de cualquier otro import que use variables de entorno
// esto asegura que lo primero que haga el programa es cargar el archivo de variables de entorno
loadEnvFile('.env'); 

// ----- Archivo configEjemplo.ts
// Da la seguridad de saber qué tipo de dato va a tener cada campo de la configuración
export interface ServerConfig { 
  mongoUri: string;
  port: number;
}

const getEnvVar = (name: string): string => {
  const value = process.env[name];
  if (!value) {
    // Es importante que el manejo de errores siempre sea explícito
    // si una variable de entorno requerida no se puede cargar el programa no debería avanzar en su ejecución
    throw new Error(`Environment variable ${name} is required but not set`);
  }
  return value;
};

export const config: ServerConfig = {
  mongoUri: getEnvVar("MONGO_URI"),
  port: parseInt(getEnvVar("SERVER_PORT"), 10),
};
```

## Archivo src/lib/Shared/Infrastructure/External.ts ❌
```typescript
import argon2 from "argon2";
import cors from "cors";
import * as dotenv from "dotenv"; // No es necesaria
import express from "express";
import mongoose from "mongoose";

export { argon2, cors, dotenv, express, mongoose };
```
Este archivo no aporta nada en absoluto. Todo esto se puede importar directamente desde los lugares donde se necesite. No recomiendo usar este tipo de archivos `barrel exports`. Es más limpio el código al importar dependencias, funciones, constantes allí donde se necesite. Los `barrel exports` tienen sentido al escribir librerías y este no es el caso. Referencia [aqui](https://tkdodo.eu/blog/please-stop-using-barrel-files).  

La dependencia `dotenv` no es necesaria para leer archivos `.env` desde `node 20.6+`. Ver ejemplo en la sección anterior respecto al archivo de configuración.

## Archivo main.ts ⚠️
Este archivo debe ser lo más limpio posible y actualmente inluye muchas cosas que deben ir en archivos separados para mejor organización del código.
```typescript
app.use("/api/business", ExpressBusinessRouter);
app.use("/api/users", ExpressUserRouter);
app.use("/api/reservations", ExpressReservationRouter);
app.use("/api/hotel", ExpressHotelRouter);
```
Esto debe ir en un archivo separado que se encargue de registrar todas las rutas de la aplicación.  

```typescript
app.use(
  (err: unknown, req: ex.Request, res: ex.Response, next: ex.NextFunction) => {
    if (err instanceof HttpError) {
      const response: ApiResponse<null> = {
        success: false,
        title: "Ocurrio un error",
        message: err.message,
        body: null,
      };

      return res.status(err.statusCode).json(response);
    }

    if (err instanceof Error) {
      console.error(err.stack);
      return res.status(500).json(err.message);
    }
    console.error(err);
    return res.status(500).json("Something wrong!");
  }
);
```
Esto podría ir en un archivo `middlewares.ts`.  

Este archivo combina `strings` en inglés y español a modo de mensajes en las respuestas: `"Ocurrio un error"` (error de ortografía) y `"Something wrong!"`. Recomiendo retornar todos los mensajes en el mismo idioma. Recomiendo máxima atención a la ortografía, sobre todo si se piensa mostrar estos mensajes en la UI.

## Definición de entidades ❌
### Ejemplo archivo `src/lib/Hotel/domain/entities/Hotel.ts`
La definición de entidades de dominio, tomando como ejemplo la entidad `Hotel`, presenta un exceso de sobreingeniería totalmente innecesario. Es como si se estuviera forzando a Typescript a comportarse como Java y además forzando la incorporación de patrones de Programación Orientada a Objetos creando complejidad innecesaria en el desarrollo, legibilidad y mantenibilidad del código, y además degradación del performance. La entidad `Hotel` también presenta un error fundamental de diseño: mezcla características de negocio (`name`, `ubication`, `score`, `status`) con características de autenticación (`email`, `password`). Idealmente la tabla `Hotel` debe representar las características de negocio del hotel, se recomienda la creación de una entidad `HotelUser` para gestionar los datos de autenticación (`email`, `password`) y de contacto del hotel. Los datos de contacto del hotel de cara a la administración del sistema (soporte técnico, representación legal, actualización de datos, etc) no necesariamente son los mismos que los datos de contacto de cara a los huéspedes del hotel.

### Problema Principal:
El código fuerza TypeScript a comportarse como Java enterprise tradicional, perdiendo las ventajas y simplicidad del ecosistema JavaScript moderno.

**Patrones Java Identificados:**
- **Value Objects excesivos**: Cada primitivo envuelto en una clase sin justificación, no tiene ningún sentido al tratarse de datos simples.
- **Interface + Class duplicación**: Patrón Java enterprise innecesario.
- **Getter/Setter verbosidad**: `getValue()` en lugar de acceso directo.
- **Type casting masivo**: Indica diseño incorrecto, degradación de performance y abre la puerta a errores en tiempo de ejecución totalmente evitables con un diseño más limpio.

### Impacto Negativo:
- **Complejidad innecesaria**: 100+ líneas para crear un objeto simple.
- **Performance degradada**: Instanciación masiva de objetos.
- **Experiencia de desarrollo pobre**: Verbosidad extrema.
- **Mantenibilidad reducida**: Demasiadas capas de abstracción.

### Recomendación:
**Adoptar filosofía TypeScript moderna:**
- Usar primitivos con tipos estratégicos.
- Value Objects solo para lógica de negocio compleja.
- Aprovechar structural typing y union types.
- Priorizar siempre la simplicidad sobre la abstracción innecesaria.

**Referencias:**
- [TypeScript Handbook - Everyday Types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html)
- [Effective TypeScript](https://effectivetypescript.com/)

### Ejemplo de entidad `Hotel` simplificada
```typescript
export type HotelPlan = 'FREE' | 'PREMIUM' | 'ENTERPRISE';
export type HotelStatus = 'ACTIVE' | 'BLOCKED' | 'SUSPENDED';

// Nótese que ya no hay campo password en esta entidad de negocio
// Tampoco hay role en esta entidad de negocio, la categoría staff se vuelve una entidad independiente acorde a su caso de uso
// Tampoco hay hotel provider data acá pues corresponde a una caraacterística relacionada con la autenticación
export interface Hotel {
  id: string; // HotelID es redundante
  name: string;
  // Email de cara a los clientes del hotel
  // diferente al email de contacto para administración en la plataforma
  email: string;
  // En la implementación actual Location solo incluye ciudad
  // hay que añadir calle, número, municipio, departamento, coordenadas (si se quiere geolocalizar en un mapa en la UI)
  location: Location;
  // Tiene que ser lista si se quiere que el hotel tenga múltiples imágenes
  // en la BD acá se guardan URLs de un sistema de almacenamiento externo
  // Optimización real sugerida: tabla Images con campos: entity_type(hotel, restaurante, etc), entity_id(id del hotel, restaurante, etc), url(url de donde se encuentra guardada la imagen)
  picture: []string;
  plan: HotelPlan;
  status: HotelStatus;
  score: number;
  createdAt: Date;
  updatedAt: Date;
  // Se sugiere añadir deletedAt como posible campo nulo para manejar los soft delete que puedan asociarse a la cancelación temporal o permanente del hotel en la plataforma
  deletedAt?: Date | null;
  freePlanEnd: Date;
  // Foreign key de una nueva tabla encargada de la autenticación, administración y gestión del hotel en la plataforma
  // también podría manejarse con una tabla intermedia si la relación es muchos a muchos entre hoteles y administradores
  hotelUserId: number;
}

// Ejemplo de instanciación sencilla sin necesidad de método constructor
const hotel: Hotel = {
  id: generateId(),
  name: "Hotel California",
  email: "info@hotelcalifornia.com",
  plan: "PREMIUM",
  status: "ACTIVE",
  score: 4.5,
  createdAt: new Date(),
};
```

### Violaciones de Seguridad CRÍTICAS:
- **Password en entidad de dominio** - Riesgo de exposición.
- **Password en respuesta toResponse()** - Violación grave de seguridad.
- **Credenciales mezcladas con datos de negocio.**

### Plan de Migración Sugerido:
1. **Fase 1**: Separar autenticación (HotelUser) del dominio Hotel.
2. **Fase 2**: Simplificar value objects críticos (mantener Email, Location).
3. **Fase 3**: Eliminar value objects innecesarios (Name, Score, etc.).
4. **Fase 4**: Refactoring de servicios y repositorios.