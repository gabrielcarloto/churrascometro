<h1 align="center">
  <strong>Churrascômetro 🍖</strong>
</h1>

<h1>
  <img src="https://ik.imagekit.io/698xlahbaqz/demo.gif?ik-sdk-version=javascript-1.4.3&updatedAt=1643761300172" />
</h1>

<h3 align="center">
  <a href="https://calculachurras.netlify.app/">Acessar a demonstração</a>
</h3>

## Índice

- [Sobre](#clipboard-sobre)
  - [Base de cálculo](#1234-base-de-cálculo)
  - [Tecnologia utilizada](#computer-tecnologia-utilizada)
- [Como baixar o projeto](#file_folder-como-baixar-o-projeto)

---

## :clipboard: Sobre

O **churrascômetro** é um simples site que calcula quanta carne e bebida você precisará para um churrasco, de acordo com o número de adultos, crianças e a duração do evento.

### :1234: Base de cálculo

|    Item     |    Duração < 6h    |    Duração >= 6h   |
| ----------- | ------------------ | ------------------ |
|  **Carne**  | 400gr por pessoa*  | 650gr por pessoa*  |
| **Cerveja** | 1200ml por pessoa  | 2000ml por pessoa  |
| **Bebidas** | 1000ml por pessoa* | 1500ml por pessoa* |

*Caso a pessoa seja uma criança, o valor é dividido por dois.

### :computer: Tecnologia utilizada

O projeto foi desenvolvido utilizando o [Svelte](https://svelte.dev/).

---

## :file_folder: Como baixar o projeto

```bash

  # Clonar o repositório
  git clone https://github.com/GabrielCarloto/churrascometro

  # Entrar no diretório
  cd churrascometro

  # Instalar as dependências
  npm install

  # Iniciar o projeto
  npm run dev

```