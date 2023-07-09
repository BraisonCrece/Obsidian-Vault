**CQRS (Command Query Responsibility Segregation)** y **Event Sourcing** son dos <span style='color:#3867d6'>patrones de diseño</span> de software que a menudo se utilizan en conjunto, aunque pueden utilizarse por separado. Ambos son particularmente comunes en la <span style='color:#3867d6'>arquitectura de microservicios</span> y en los sistemas de procesamiento de <span style='color:#3867d6'>transacciones en tiempo real</span>.

## <span style='color:#8854d0'>CQRS</span> 
Es un patrón de diseño que sugiere que los objetos de un software **no deberían tener las mismas interfaces para modificar y leer datos**. 

En otras palabras, las operaciones de **actualización** (o "commands") y las operaciones de **lectura** (o "queries") deben ser manejadas por diferentes componentes. Esto puede hacer que **el sistema sea más escalable y fácil de mantener**. 

- Comandos --> Create, Update, Delete
	- Deben ser colocados en una cola para procesamiento asíncrono
- Queries --> Read
	- Nunca jamás deben modificar la base de datos

Por qué usar CQRS?
A pesar de que implica una mayor complejidad en el código, nos brinda distintos beneficios.

Implementar CQRS puede maximizar el **rendimiento**, la **escalabilidad** y la 
**seguridad**.
1. Incrementamos el foco en problemas concretos. El componente es optimizado tanto para lidiar con comandos como con queries.
2. Nos permite hacer despliegues separados para comandos y queries.
3. Nos permite escalar por separado según el requerimiento.
4. Nos permite elegir diferentes tipos de bases de datos para comandos y queries.
<svg version="1.1" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 900.2839717741012 487.8536787767795" width="900.2839717741012" height="487.8536787767795">
  <!-- svg-source:excalidraw -->
  
  <defs>
    <style class="style-fonts">
      @font-face {
        font-family: "Virgil";
        src: url("https://excalidraw.com/Virgil.woff2");
      }
      @font-face {
        font-family: "Cascadia";
        src: url("https://excalidraw.com/Cascadia.woff2");
      }
    </style>
  </defs>
  <rect x="0" y="0" width="900.2839717741012" height="487.8536787767795" fill="#ffffff"></rect><g stroke-linecap="round" transform="translate(10 121.43406933410057) rotate(0 95 69)"><path d="M0 0 C69.58 0, 139.16 0, 190 0 M0 0 C75.42 0, 150.83 0, 190 0 M190 0 C190 27.96, 190 55.93, 190 138 M190 0 C190 37.53, 190 75.06, 190 138 M190 138 C149.01 138, 108.02 138, 0 138 M190 138 C124.58 138, 59.17 138, 0 138 M0 138 C0 109.41, 0 80.82, 0 0 M0 138 C0 86.33, 0 34.67, 0 0" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g><g transform="translate(51 178.43406933410057) rotate(0 52.7734375 11.5)"><text x="0" y="0" font-family="Helvetica, Segoe UI Emoji" font-size="20px" fill="#1e1e1e" text-anchor="start" style="white-space: pre;" direction="ltr" dominant-baseline="text-before-edge">COMMAND</text></g><g stroke-linecap="round" transform="translate(11 327.43406933410057) rotate(0 95 69)"><path d="M0 0 C49.73 0, 99.47 0, 190 0 M0 0 C70.87 0, 141.74 0, 190 0 M190 0 C190 38.31, 190 76.61, 190 138 M190 0 C190 41.91, 190 83.82, 190 138 M190 138 C149.23 138, 108.45 138, 0 138 M190 138 C114.51 138, 39.03 138, 0 138 M0 138 C0 89.82, 0 41.64, 0 0 M0 138 C0 104.07, 0 70.15, 0 0" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g><g transform="translate(60 386.43406933410057) rotate(0 45.009765625 11.5)"><text x="0" y="0" font-family="Helvetica, Segoe UI Emoji" font-size="20px" fill="#1e1e1e" text-anchor="start" style="white-space: pre;" direction="ltr" dominant-baseline="text-before-edge">QUERIES</text></g><g stroke-linecap="round" transform="translate(656.497663210658 109.72024177860891) rotate(0 116.5 19.5)"><path d="M233 19.5 C233 20.18, 232.79 20.86, 232.36 21.54 C231.94 22.21, 231.3 22.89, 230.45 23.55 C229.61 24.22, 228.55 24.88, 227.3 25.53 C226.04 26.17, 224.58 26.81, 222.93 27.43 C221.28 28.05, 219.42 28.66, 217.39 29.25 C215.36 29.84, 213.14 30.41, 210.75 30.96 C208.36 31.51, 205.79 32.04, 203.08 32.55 C200.36 33.05, 197.47 33.54, 194.45 33.99 C191.44 34.45, 188.26 34.88, 184.98 35.28 C181.69 35.68, 178.27 36.05, 174.75 36.39 C171.23 36.73, 167.59 37.04, 163.88 37.31 C160.18 37.59, 156.36 37.84, 152.5 38.05 C148.64 38.26, 144.69 38.43, 140.72 38.57 C136.75 38.72, 132.71 38.82, 128.68 38.89 C124.64 38.96, 120.56 39, 116.5 39 C112.44 39, 108.36 38.96, 104.32 38.89 C100.29 38.82, 96.25 38.72, 92.28 38.57 C88.31 38.43, 84.36 38.26, 80.5 38.05 C76.64 37.84, 72.82 37.59, 69.12 37.31 C65.41 37.04, 61.77 36.73, 58.25 36.39 C54.73 36.05, 51.31 35.68, 48.02 35.28 C44.74 34.88, 41.56 34.45, 38.55 33.99 C35.53 33.54, 32.64 33.05, 29.92 32.55 C27.21 32.04, 24.64 31.51, 22.25 30.96 C19.86 30.41, 17.64 29.84, 15.61 29.25 C13.58 28.66, 11.72 28.05, 10.07 27.43 C8.42 26.81, 6.96 26.17, 5.7 25.53 C4.45 24.88, 3.39 24.22, 2.55 23.55 C1.7 22.89, 1.06 22.21, 0.64 21.54 C0.21 20.86, 0 20.18, 0 19.5 C0 18.82, 0.21 18.14, 0.64 17.46 C1.06 16.79, 1.7 16.11, 2.55 15.45 C3.39 14.78, 4.45 14.12, 5.7 13.47 C6.96 12.83, 8.42 12.19, 10.07 11.57 C11.72 10.95, 13.58 10.34, 15.61 9.75 C17.64 9.16, 19.86 8.59, 22.25 8.04 C24.64 7.49, 27.21 6.96, 29.92 6.45 C32.64 5.95, 35.53 5.46, 38.55 5.01 C41.56 4.55, 44.74 4.12, 48.02 3.72 C51.31 3.32, 54.73 2.95, 58.25 2.61 C61.77 2.27, 65.41 1.96, 69.12 1.69 C72.82 1.41, 76.64 1.16, 80.5 0.95 C84.36 0.74, 88.31 0.57, 92.28 0.43 C96.25 0.28, 100.29 0.18, 104.32 0.11 C108.36 0.04, 112.44 0, 116.5 0 C120.56 0, 124.64 0.04, 128.68 0.11 C132.71 0.18, 136.75 0.28, 140.72 0.43 C144.69 0.57, 148.64 0.74, 152.5 0.95 C156.36 1.16, 160.18 1.41, 163.88 1.69 C167.59 1.96, 171.23 2.27, 174.75 2.61 C178.27 2.95, 181.69 3.32, 184.98 3.72 C188.26 4.12, 191.44 4.55, 194.45 5.01 C197.47 5.46, 200.36 5.95, 203.08 6.45 C205.79 6.96, 208.36 7.49, 210.75 8.04 C213.14 8.59, 215.36 9.16, 217.39 9.75 C219.42 10.34, 221.28 10.95, 222.93 11.57 C224.58 12.19, 226.04 12.83, 227.3 13.47 C228.55 14.12, 229.61 14.78, 230.45 15.45 C231.3 16.11, 231.94 16.79, 232.36 17.46 C232.79 18.14, 232.89 19.16, 233 19.5 C233.11 19.84, 233.11 19.16, 233 19.5" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g><g stroke-linecap="round"><g transform="translate(656.430304969792 129.32848268401267) rotate(0 0 56.5)"><path d="M0 0 C0 39.93, 0 79.87, 0 113 M0 0 C0 38.29, 0 76.57, 0 113" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g></g><mask></mask><g stroke-linecap="round"><g transform="translate(889.5745978674095 129.0224478422457) rotate(0 0 56.5)"><path d="M0 0 C0 38.38, 0 76.76, 0 113 M0 0 C0 44.24, 0 88.48, 0 113" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g></g><mask></mask><g stroke-linecap="round" transform="translate(656.7123010827484 222.1904867540304) rotate(0 116.5 19.5)"><path d="M233 19.5 C233 20.18, 232.79 20.86, 232.36 21.54 C231.94 22.21, 231.3 22.89, 230.45 23.55 C229.61 24.22, 228.55 24.88, 227.3 25.53 C226.04 26.17, 224.58 26.81, 222.93 27.43 C221.28 28.05, 219.42 28.66, 217.39 29.25 C215.36 29.84, 213.14 30.41, 210.75 30.96 C208.36 31.51, 205.79 32.04, 203.08 32.55 C200.36 33.05, 197.47 33.54, 194.45 33.99 C191.44 34.45, 188.26 34.88, 184.98 35.28 C181.69 35.68, 178.27 36.05, 174.75 36.39 C171.23 36.73, 167.59 37.04, 163.88 37.31 C160.18 37.59, 156.36 37.84, 152.5 38.05 C148.64 38.26, 144.69 38.43, 140.72 38.57 C136.75 38.72, 132.71 38.82, 128.68 38.89 C124.64 38.96, 120.56 39, 116.5 39 C112.44 39, 108.36 38.96, 104.32 38.89 C100.29 38.82, 96.25 38.72, 92.28 38.57 C88.31 38.43, 84.36 38.26, 80.5 38.05 C76.64 37.84, 72.82 37.59, 69.12 37.31 C65.41 37.04, 61.77 36.73, 58.25 36.39 C54.73 36.05, 51.31 35.68, 48.02 35.28 C44.74 34.88, 41.56 34.45, 38.55 33.99 C35.53 33.54, 32.64 33.05, 29.92 32.55 C27.21 32.04, 24.64 31.51, 22.25 30.96 C19.86 30.41, 17.64 29.84, 15.61 29.25 C13.58 28.66, 11.72 28.05, 10.07 27.43 C8.42 26.81, 6.96 26.17, 5.7 25.53 C4.45 24.88, 3.39 24.22, 2.55 23.55 C1.7 22.89, 1.06 22.21, 0.64 21.54 C0.21 20.86, 0 20.18, 0 19.5 C0 18.82, 0.21 18.14, 0.64 17.46 C1.06 16.79, 1.7 16.11, 2.55 15.45 C3.39 14.78, 4.45 14.12, 5.7 13.47 C6.96 12.83, 8.42 12.19, 10.07 11.57 C11.72 10.95, 13.58 10.34, 15.61 9.75 C17.64 9.16, 19.86 8.59, 22.25 8.04 C24.64 7.49, 27.21 6.96, 29.92 6.45 C32.64 5.95, 35.53 5.46, 38.55 5.01 C41.56 4.55, 44.74 4.12, 48.02 3.72 C51.31 3.32, 54.73 2.95, 58.25 2.61 C61.77 2.27, 65.41 1.96, 69.12 1.69 C72.82 1.41, 76.64 1.16, 80.5 0.95 C84.36 0.74, 88.31 0.57, 92.28 0.43 C96.25 0.28, 100.29 0.18, 104.32 0.11 C108.36 0.04, 112.44 0, 116.5 0 C120.56 0, 124.64 0.04, 128.68 0.11 C132.71 0.18, 136.75 0.28, 140.72 0.43 C144.69 0.57, 148.64 0.74, 152.5 0.95 C156.36 1.16, 160.18 1.41, 163.88 1.69 C167.59 1.96, 171.23 2.27, 174.75 2.61 C178.27 2.95, 181.69 3.32, 184.98 3.72 C188.26 4.12, 191.44 4.55, 194.45 5.01 C197.47 5.46, 200.36 5.95, 203.08 6.45 C205.79 6.96, 208.36 7.49, 210.75 8.04 C213.14 8.59, 215.36 9.16, 217.39 9.75 C219.42 10.34, 221.28 10.95, 222.93 11.57 C224.58 12.19, 226.04 12.83, 227.3 13.47 C228.55 14.12, 229.61 14.78, 230.45 15.45 C231.3 16.11, 231.94 16.79, 232.36 17.46 C232.79 18.14, 232.89 19.16, 233 19.5 C233.11 19.84, 233.11 19.16, 233 19.5" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g><g stroke-linecap="round" transform="translate(656.9130115873544 210.42233780996355) rotate(0 116.09688917863946 15.668564662606421)"><path d="M0 0 L232.19 0 L232.19 31.34 L0 31.34" stroke="none" stroke-width="0" fill="#ffffff"></path><path d="M0 0 C91.34 0, 182.68 0, 232.19 0 M0 0 C91.8 0, 183.59 0, 232.19 0 M232.19 0 C232.19 12.35, 232.19 24.7, 232.19 31.34 M232.19 0 C232.19 8.03, 232.19 16.05, 232.19 31.34 M232.19 31.34 C141.3 31.34, 50.4 31.34, 0 31.34 M232.19 31.34 C168.11 31.34, 104.02 31.34, 0 31.34 M0 31.34 C0 24.75, 0 18.17, 0 0 M0 31.34 C0 21.84, 0 12.35, 0 0" stroke="transparent" stroke-width="1" fill="none"></path></g><g stroke-linecap="round" transform="translate(657.0693339020108 326.38343380135797) rotate(0 116.5 19.5)"><path d="M233 19.5 C233 20.18, 232.79 20.86, 232.36 21.54 C231.94 22.21, 231.3 22.89, 230.45 23.55 C229.61 24.22, 228.55 24.88, 227.3 25.53 C226.04 26.17, 224.58 26.81, 222.93 27.43 C221.28 28.05, 219.42 28.66, 217.39 29.25 C215.36 29.84, 213.14 30.41, 210.75 30.96 C208.36 31.51, 205.79 32.04, 203.08 32.55 C200.36 33.05, 197.47 33.54, 194.45 33.99 C191.44 34.45, 188.26 34.88, 184.98 35.28 C181.69 35.68, 178.27 36.05, 174.75 36.39 C171.23 36.73, 167.59 37.04, 163.88 37.31 C160.18 37.59, 156.36 37.84, 152.5 38.05 C148.64 38.26, 144.69 38.43, 140.72 38.57 C136.75 38.72, 132.71 38.82, 128.68 38.89 C124.64 38.96, 120.56 39, 116.5 39 C112.44 39, 108.36 38.96, 104.32 38.89 C100.29 38.82, 96.25 38.72, 92.28 38.57 C88.31 38.43, 84.36 38.26, 80.5 38.05 C76.64 37.84, 72.82 37.59, 69.12 37.31 C65.41 37.04, 61.77 36.73, 58.25 36.39 C54.73 36.05, 51.31 35.68, 48.02 35.28 C44.74 34.88, 41.56 34.45, 38.55 33.99 C35.53 33.54, 32.64 33.05, 29.92 32.55 C27.21 32.04, 24.64 31.51, 22.25 30.96 C19.86 30.41, 17.64 29.84, 15.61 29.25 C13.58 28.66, 11.72 28.05, 10.07 27.43 C8.42 26.81, 6.96 26.17, 5.7 25.53 C4.45 24.88, 3.39 24.22, 2.55 23.55 C1.7 22.89, 1.06 22.21, 0.64 21.54 C0.21 20.86, 0 20.18, 0 19.5 C0 18.82, 0.21 18.14, 0.64 17.46 C1.06 16.79, 1.7 16.11, 2.55 15.45 C3.39 14.78, 4.45 14.12, 5.7 13.47 C6.96 12.83, 8.42 12.19, 10.07 11.57 C11.72 10.95, 13.58 10.34, 15.61 9.75 C17.64 9.16, 19.86 8.59, 22.25 8.04 C24.64 7.49, 27.21 6.96, 29.92 6.45 C32.64 5.95, 35.53 5.46, 38.55 5.01 C41.56 4.55, 44.74 4.12, 48.02 3.72 C51.31 3.32, 54.73 2.95, 58.25 2.61 C61.77 2.27, 65.41 1.96, 69.12 1.69 C72.82 1.41, 76.64 1.16, 80.5 0.95 C84.36 0.74, 88.31 0.57, 92.28 0.43 C96.25 0.28, 100.29 0.18, 104.32 0.11 C108.36 0.04, 112.44 0, 116.5 0 C120.56 0, 124.64 0.04, 128.68 0.11 C132.71 0.18, 136.75 0.28, 140.72 0.43 C144.69 0.57, 148.64 0.74, 152.5 0.95 C156.36 1.16, 160.18 1.41, 163.88 1.69 C167.59 1.96, 171.23 2.27, 174.75 2.61 C178.27 2.95, 181.69 3.32, 184.98 3.72 C188.26 4.12, 191.44 4.55, 194.45 5.01 C197.47 5.46, 200.36 5.95, 203.08 6.45 C205.79 6.96, 208.36 7.49, 210.75 8.04 C213.14 8.59, 215.36 9.16, 217.39 9.75 C219.42 10.34, 221.28 10.95, 222.93 11.57 C224.58 12.19, 226.04 12.83, 227.3 13.47 C228.55 14.12, 229.61 14.78, 230.45 15.45 C231.3 16.11, 231.94 16.79, 232.36 17.46 C232.79 18.14, 232.89 19.16, 233 19.5 C233.11 19.84, 233.11 19.16, 233 19.5" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g><g stroke-linecap="round"><g transform="translate(657.0019756611449 345.9916747067617) rotate(0 0 56.49999999999999)"><path d="M0 0 C0 30.25, 0 60.51, 0 113 M0 0 C0 31.8, 0 63.59, 0 113" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g></g><mask></mask><g stroke-linecap="round"><g transform="translate(890.1462685587624 345.68563986499476) rotate(0 0 56.49999999999999)"><path d="M0 0 C0 39.15, 0 78.29, 0 113 M0 0 C0 36.29, 0 72.59, 0 113" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g></g><mask></mask><g stroke-linecap="round" transform="translate(657.2839717741012 438.8536787767795) rotate(0 116.5 19.5)"><path d="M233 19.5 C233 20.18, 232.79 20.86, 232.36 21.54 C231.94 22.21, 231.3 22.89, 230.45 23.55 C229.61 24.22, 228.55 24.88, 227.3 25.53 C226.04 26.17, 224.58 26.81, 222.93 27.43 C221.28 28.05, 219.42 28.66, 217.39 29.25 C215.36 29.84, 213.14 30.41, 210.75 30.96 C208.36 31.51, 205.79 32.04, 203.08 32.55 C200.36 33.05, 197.47 33.54, 194.45 33.99 C191.44 34.45, 188.26 34.88, 184.98 35.28 C181.69 35.68, 178.27 36.05, 174.75 36.39 C171.23 36.73, 167.59 37.04, 163.88 37.31 C160.18 37.59, 156.36 37.84, 152.5 38.05 C148.64 38.26, 144.69 38.43, 140.72 38.57 C136.75 38.72, 132.71 38.82, 128.68 38.89 C124.64 38.96, 120.56 39, 116.5 39 C112.44 39, 108.36 38.96, 104.32 38.89 C100.29 38.82, 96.25 38.72, 92.28 38.57 C88.31 38.43, 84.36 38.26, 80.5 38.05 C76.64 37.84, 72.82 37.59, 69.12 37.31 C65.41 37.04, 61.77 36.73, 58.25 36.39 C54.73 36.05, 51.31 35.68, 48.02 35.28 C44.74 34.88, 41.56 34.45, 38.55 33.99 C35.53 33.54, 32.64 33.05, 29.92 32.55 C27.21 32.04, 24.64 31.51, 22.25 30.96 C19.86 30.41, 17.64 29.84, 15.61 29.25 C13.58 28.66, 11.72 28.05, 10.07 27.43 C8.42 26.81, 6.96 26.17, 5.7 25.53 C4.45 24.88, 3.39 24.22, 2.55 23.55 C1.7 22.89, 1.06 22.21, 0.64 21.54 C0.21 20.86, 0 20.18, 0 19.5 C0 18.82, 0.21 18.14, 0.64 17.46 C1.06 16.79, 1.7 16.11, 2.55 15.45 C3.39 14.78, 4.45 14.12, 5.7 13.47 C6.96 12.83, 8.42 12.19, 10.07 11.57 C11.72 10.95, 13.58 10.34, 15.61 9.75 C17.64 9.16, 19.86 8.59, 22.25 8.04 C24.64 7.49, 27.21 6.96, 29.92 6.45 C32.64 5.95, 35.53 5.46, 38.55 5.01 C41.56 4.55, 44.74 4.12, 48.02 3.72 C51.31 3.32, 54.73 2.95, 58.25 2.61 C61.77 2.27, 65.41 1.96, 69.12 1.69 C72.82 1.41, 76.64 1.16, 80.5 0.95 C84.36 0.74, 88.31 0.57, 92.28 0.43 C96.25 0.28, 100.29 0.18, 104.32 0.11 C108.36 0.04, 112.44 0, 116.5 0 C120.56 0, 124.64 0.04, 128.68 0.11 C132.71 0.18, 136.75 0.28, 140.72 0.43 C144.69 0.57, 148.64 0.74, 152.5 0.95 C156.36 1.16, 160.18 1.41, 163.88 1.69 C167.59 1.96, 171.23 2.27, 174.75 2.61 C178.27 2.95, 181.69 3.32, 184.98 3.72 C188.26 4.12, 191.44 4.55, 194.45 5.01 C197.47 5.46, 200.36 5.95, 203.08 6.45 C205.79 6.96, 208.36 7.49, 210.75 8.04 C213.14 8.59, 215.36 9.16, 217.39 9.75 C219.42 10.34, 221.28 10.95, 222.93 11.57 C224.58 12.19, 226.04 12.83, 227.3 13.47 C228.55 14.12, 229.61 14.78, 230.45 15.45 C231.3 16.11, 231.94 16.79, 232.36 17.46 C232.79 18.14, 232.89 19.16, 233 19.5 C233.11 19.84, 233.11 19.16, 233 19.5" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g><g stroke-linecap="round" transform="translate(657.4846822787073 427.0855298327126) rotate(0 116.09688917863946 15.668564662606428)"><path d="M0 0 L232.19 0 L232.19 31.34 L0 31.34" stroke="none" stroke-width="0" fill="#ffffff"></path><path d="M0 0 C61.75 0, 123.49 0, 232.19 0 M0 0 C55.38 0, 110.77 0, 232.19 0 M232.19 0 C232.19 12.48, 232.19 24.97, 232.19 31.34 M232.19 0 C232.19 6.46, 232.19 12.93, 232.19 31.34 M232.19 31.34 C155.32 31.34, 78.44 31.34, 0 31.34 M232.19 31.34 C180.62 31.34, 129.04 31.34, 0 31.34 M0 31.34 C0 23.85, 0 16.37, 0 0 M0 31.34 C0 22.57, 0 13.8, 0 0" stroke="transparent" stroke-width="1" fill="none"></path></g><g stroke-linecap="round" transform="translate(332.9524416812359 10) rotate(0 95 69)"><path d="M0 0 C61.19 0, 122.38 0, 190 0 M0 0 C74.28 0, 148.56 0, 190 0 M190 0 C190 45.57, 190 91.14, 190 138 M190 0 C190 46.36, 190 92.72, 190 138 M190 138 C140.23 138, 90.45 138, 0 138 M190 138 C115.19 138, 40.37 138, 0 138 M0 138 C0 89.12, 0 40.24, 0 0 M0 138 C0 102.55, 0 67.1, 0 0" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g><g transform="translate(387.3658018788591 68.03179693827872) rotate(0 40.009765625 11.5)"><text x="0" y="0" font-family="Helvetica, Segoe UI Emoji" font-size="20px" fill="#1e1e1e" text-anchor="start" style="white-space: pre;" direction="ltr" dominant-baseline="text-before-edge">EVENTS</text></g><g stroke-linecap="round"><g transform="translate(202.93691645936417 189.47847114123527) rotate(0 63.45551170414058 -56.232933136189615)"><path d="M0 0 C13.16 -12.71, 26.31 -25.42, 60.88 -58.81 M0 0 C13.98 -13.51, 27.96 -27.02, 60.88 -58.81 M60.88 -58.81 C75.19 -70.44, 89.5 -82.07, 126.91 -112.47 M60.88 -58.81 C77.59 -72.39, 94.3 -85.97, 126.91 -112.47" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g><g transform="translate(202.93691645936417 189.47847114123527) rotate(0 63.45551170414058 -56.232933136189615)"><path d="M111.5 -86.73 C114.83 -92.29, 118.16 -97.85, 126.91 -112.47 M111.5 -86.73 C115.04 -92.64, 118.58 -98.55, 126.91 -112.47" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g><g transform="translate(202.93691645936417 189.47847114123527) rotate(0 63.45551170414058 -56.232933136189615)"><path d="M98.56 -102.65 C104.69 -104.77, 110.81 -106.89, 126.91 -112.47 M98.56 -102.65 C105.07 -104.91, 111.58 -107.16, 126.91 -112.47" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g></g><mask></mask><g stroke-linecap="round"><g transform="translate(523.8257642640426 81.13979262197086) rotate(0 67.58269945725539 51.58984691393542)"><path d="M0 0 C31.25 23.86, 62.51 47.71, 135.17 103.18 M0 0 C47.97 36.62, 95.93 73.23, 135.17 103.18" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g><g transform="translate(523.8257642640426 81.13979262197086) rotate(0 67.58269945725539 51.58984691393542)"><path d="M106.53 94.23 C113.15 96.3, 119.77 98.37, 135.17 103.18 M106.53 94.23 C116.69 97.41, 126.85 100.58, 135.17 103.18" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g><g transform="translate(523.8257642640426 81.13979262197086) rotate(0 67.58269945725539 51.58984691393542)"><path d="M118.98 77.92 C122.72 83.76, 126.47 89.6, 135.17 103.18 M118.98 77.92 C124.73 86.88, 130.47 95.85, 135.17 103.18" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g></g><mask></mask><g stroke-linecap="round"><g transform="translate(202.93691645936417 402.02864042664925) rotate(0 226.9953264213159 2.0635938765574338)"><path d="M0 0 C178.65 1.62, 357.3 3.25, 453.99 4.13 M0 0 C149.66 1.36, 299.32 2.72, 453.99 4.13" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g><g transform="translate(202.93691645936417 402.02864042664925) rotate(0 226.9953264213159 2.0635938765574338)"><path d="M425.71 14.13 C436.84 10.19, 447.97 6.26, 453.99 4.13 M425.71 14.13 C435.03 10.83, 444.35 7.54, 453.99 4.13" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g><g transform="translate(202.93691645936417 402.02864042664925) rotate(0 226.9953264213159 2.0635938765574338)"><path d="M425.89 -6.39 C436.95 -2.25, 448.01 1.89, 453.99 4.13 M425.89 -6.39 C435.16 -2.92, 444.42 0.54, 453.99 4.13" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g></g><mask></mask><g transform="translate(718.8353855987185 184.3194864498417) rotate(0 51.865234375 11.5)"><text x="0" y="0" font-family="Helvetica, Segoe UI Emoji" font-size="20px" fill="#1e1e1e" text-anchor="start" style="white-space: pre;" direction="ltr" dominant-baseline="text-before-edge">DATABASE</text></g><g transform="translate(719.8671825369971 400.99684348837053) rotate(0 51.865234375 11.5)"><text x="0" y="0" font-family="Helvetica, Segoe UI Emoji" font-size="20px" fill="#1e1e1e" text-anchor="start" style="white-space: pre;" direction="ltr" dominant-baseline="text-before-edge">DATABASE</text></g><g stroke-linecap="round"><g transform="translate(721.7140424799715 343.5121873301763) rotate(0 0.34738668494928504 -40.6442421390671)"><path d="M0 0 C0.21 -24.47, 0.42 -48.94, 0.69 -81.29 M0 0 C0.15 -17.03, 0.29 -34.06, 0.69 -81.29" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g><g transform="translate(721.7140424799715 343.5121873301763) rotate(0 0.34738668494928504 -40.6442421390671)"><path d="M10.71 -53.01 C7.7 -61.52, 4.68 -70.04, 0.69 -81.29 M10.71 -53.01 C8.62 -58.93, 6.52 -64.86, 0.69 -81.29" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g><g transform="translate(721.7140424799715 343.5121873301763) rotate(0 0.34738668494928504 -40.6442421390671)"><path d="M-9.81 -53.19 C-6.65 -61.65, -3.48 -70.11, 0.69 -81.29 M-9.81 -53.19 C-7.61 -59.07, -5.41 -64.96, 0.69 -81.29" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g></g><mask></mask><g stroke-linecap="round"><g transform="translate(820.3718610055702 260.1393829423464) rotate(0 0.8684667123732481 41.16532216649104)"><path d="M0 0 C0.42 20.14, 0.85 40.27, 1.74 82.33 M0 0 C0.52 24.42, 1.03 48.84, 1.74 82.33" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g><g transform="translate(820.3718610055702 260.1393829423464) rotate(0 0.8684667123732481 41.16532216649104)"><path d="M-9.12 54.36 C-6.46 61.2, -3.81 68.04, 1.74 82.33 M-9.12 54.36 C-5.9 62.66, -2.68 70.95, 1.74 82.33" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g><g transform="translate(820.3718610055702 260.1393829423464) rotate(0 0.8684667123732481 41.16532216649104)"><path d="M11.4 53.93 C9.04 60.88, 6.67 67.82, 1.74 82.33 M11.4 53.93 C8.53 62.35, 5.67 70.78, 1.74 82.33" stroke="#1e1e1e" stroke-width="1" fill="none"></path></g></g><mask></mask><g transform="translate(734.2199631381459 273.5557568607237) rotate(0 36.01953125 18.4)"><text x="36.01953125" y="0" font-family="Helvetica, Segoe UI Emoji" font-size="16px" fill="#1971c2" text-anchor="middle" style="white-space: pre;" direction="ltr" dominant-baseline="text-before-edge">sync </text><text x="36.01953125" y="18.4" font-family="Helvetica, Segoe UI Emoji" font-size="16px" fill="#1971c2" text-anchor="middle" style="white-space: pre;" direction="ltr" dominant-baseline="text-before-edge">constantly</text></g></svg>

## ¿Qué es un command?
Pues no es más que un <span style='color:#3867d6'>DTO</span> (**Data Transfer Object**)
Un DTO no es más que una clase plana, con atributos primitivos y sin nada de lógica, que nos sirve **para llevar los datos de un punto A a un punto B**. Estos puntos, <span style='color:#3867d6'>A</span> y <span style='color:#3867d6'>B</span> no son otra cosa más que <span style='color:#eb3b5a'>capas</span> de nuestra aplicación.

Este objeto peculiar no contiene lógica porque en algún punto de su ciclo de vida, va a ser necesario serializarlo para comunicación entre sistemas. 
Y de llevar lógica, en su determinado lenguaje de programación, perderíamos una de las cualidades más valiosas de CQRS, su abstracción del lenguaje.

Ejemplo de una clase Command:
```php
final class RenameUserCommand extends Command
{
    private $userId;
    private $newName;

	public function __construct(string $userId, string $newName)
    {
        $this->userId  = $userId;
        $this->newName = $newName;
    }
    
	public function userId(): string
    {
        return $this->userId;
    }
    
    public function newName(): string
    {
        return $this->newName;
    }
}
```

Como podemos apreciar, esta clase solo tiene atributos en tipos primitivos, y getters, pues esta debe ser inmutable.

Este Command nos llegaría a nuestro <span style='color:#8854d0'>CommandHandler</span>, el cuál crearía dos **Value Objects:** <span style='color:#eb3b5a'>UserId</span> y <span style='color:#eb3b5a'>UserName</span> (de esta forma estaríamos validando que los valores del Command cumplen con los requisitos de nuestro dominio).

Luego el **CommandHandler enviaría estos datos al caso de uso**, el cuál ya se encargaría de ir al Repositorio para recuperar el usuario, cambiarle el nombre, volverlo a persistir y luego lanzar el evento de nombre modificado.

![[Pasted image 20230610124240.png]]

Que acciones HTTP corresponden a commands?
Pues las que modifican el recurso:
- POST
- PUT
- PATCH
- DELETE







## <span style='color:#8854d0'>Event Sourcing</span>
Es un patrón de diseño en el que los **cambios en el estado** de una aplicación **se almacenan como una secuencia de eventos**. 

En lugar de actualizar un registro en una base de datos para reflejar los cambios en el estado, con Event Sourcing se registra cada evento (cambio de estado) que ocurre. Esto tiene la ventaja de que puede tener un registro completo de todo lo que ha sucedido en la aplicación, lo cual puede ser útil para la auditoria, la depuración y el análisis de tendencias.

Cuando se utilizan juntos, CQRS y Event Sourcing pueden proporcionar un sistema muy escalable que puede manejar grandes volúmenes de datos y ofrecer un alto rendimiento, a la vez que mantiene un registro completo de todos los cambios en el estado de la aplicación. Sin embargo, la complejidad adicional significa que no son adecuados para todos los proyectos.

Base de datos para Eventos:
- **Flujos de eventos** - un flujo por entidad
- Puede **suscribirse a eventos** - suscripciones persistentes
- Desplazarse por el flujo de eventos en cualquier dirección para el **análisis y la previsión** - hacia delante o hacia atrás
- **Rastree** los eventos hasta sus orígenes
- **Indexación**
- **Proyecciones** - integradas y definidas por el usuario


## <mark style="background: #D2B3FFA6;">EJEMPLOS PRÁCTICOS</mark>

### <span style='color:#4b6584'>CQRS</span>
Consideremos un servicio de biblioteca en línea. Este servicio puede tener dos tipos de interacciones: 
- los usuarios pueden **sacar libros** (una operación de modificación o "**command**")
- los usuarios pueden **buscar libros** (una operación de lectura o "**query**").

En un diseño tradicional, podrías tener un único objeto "Libro" que maneje ambas operaciones. Sin embargo, en un sistema que utiliza CQRS, **dividirías estas responsabilidades en dos objetos separados**: 
- uno para manejar las operaciones de <span style='color:#fa8231'>modificación</span>
- otro para manejar las operaciones de <span style='color:#20bf6b'>lectura</span>.

Así, podrías tener un objeto <span style='color:#fa8231'>BookCommandService</span> que maneje las operaciones de modificación, como sacar un libro. Cuando un usuario saca un libro, el <span style='color:#fa8231'>BookCommandService</span> se encarga de modificar el estado del sistema para reflejar que el libro ha sido sacado.

Por otro lado, tendrías un objeto <span style='color:#20bf6b'>BookQueryService</span> que maneje las operaciones de lectura, como buscar un libro. Cuando un usuario busca un libro, el <span style='color:#20bf6b'>BookQueryService</span> se encarga de leer el estado del sistema y devolver los resultados de la búsqueda.

Estos dos servicios pueden incluso tener diferentes modelos de datos y bases de datos que se optimizan para sus operaciones respectivas. 


### <span style='color:#4b6584'>Event Sourcing</span>
Un ejemplo práctico de Event Sourcing podría ser un sistema bancario.

Supón que tienes una cuenta bancaria con las siguientes operaciones:

1. Se abre la cuenta con un depósito inicial de $100.
2. Se depositan $50 en la cuenta.
3. Se retiran $30 de la cuenta.

En un sistema tradicional, simplemente se almacenaría el estado final de la cuenta, es decir, el saldo actual de $120. Sin embargo, en un sistema que utiliza Event Sourcing, cada uno de los cambios en el saldo de la cuenta se almacenaría como un evento. Así, tendríamos algo como esto:

1. **Evento de Apertura** de Cuenta: $100
2. **Evento de Depósito**: $50
3. **Evento de Retiro**: -$30

Cada uno de estos eventos representaría un cambio en el estado de la cuenta. Para obtener el estado actual de la cuenta (es decir, el saldo actual), <span style='color:#3867d6'>el sistema simplemente "reproduce" todos los eventos en orden</span>.

Este enfoque tiene varias ventajas. Por un lado, proporciona un registro completo de todos los cambios en el estado de la cuenta, lo cual es útil para la auditoria y la resolución de conflictos. Por otro lado, también puede facilitar el desarrollo de ciertas características, como mostrar al usuario un historial completo de sus transacciones.