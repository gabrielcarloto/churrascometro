<!--
	TODO:
	- [ ] Darkmode
  - [ ] Acessibilidade
-->

<script>
	import { fade, slide } from 'svelte/transition';

	let bebidaPlural;
	let inputAdultos;
	let inputCriancas;
	let inputDuracao;
	let carne;
	let bebida;
	let cerveja;
	let carneFinal;
  let bebidaFinal;
  let cervejaFinal;
	let modal = false;

  // calcula os valores do box e da margem do botão 
  // para não ter problemas ao abrir o teclado em dispositivos mobile
  const windowHeight = window.innerHeight;
  const boxHeight = windowHeight / 2;
  const buttonMarginTop = boxHeight / 5.5;

  function calculo(e) {
    // previne o comportamento padrão do formulário
    e.preventDefault();

    // pega os dados do formulário
    const formData = new FormData(e.target);

    // cria consts para os dados do formulário
    const adultos = formData.get("adultos");
    const criancas = formData.get("criancas");
    const duracao = formData.get("duracao");

		// limpa os inputs
		inputAdultos.value = '';
		inputCriancas.value = '';
		inputDuracao.value = '';

    // calcula a quantia de carne e bebidas com base nos dados do formulário
    /*
				CARNE -> 400 gr por pessoa                   + de 6 horas -> 650 gr
				CERVEJA -> 1200 ml por pessoa                + de 6 horas -> 2000 ml
				REFRIGERANTE/ÁGUA -> 1000 ml por pessoa      + de 6 horas -> 1500 ml
				
				crianças valem por 0,5
			*/

    function carnePorPessoa(duracao) {
      if (duracao >= 6) {
        return 650;
      } else {
        return 400;
      }
    }

    function cervejaPorPessoa(duracao) {
      if (duracao >= 6) {
        return 2000;
      } else {
        return 1200;
      }
    }

    function bebidaPorPessoa(duracao) {
      if (duracao >= 6) {
        return 1500;
      } else {
        return 1000;
      }
    }

    carne = adultos * carnePorPessoa(duracao) + criancas * carnePorPessoa(duracao) * 0.5;
    cerveja = adultos * cervejaPorPessoa(duracao);
    bebida = adultos * bebidaPorPessoa(duracao) + criancas * bebidaPorPessoa(duracao) * 0.5;

		carneFinal = carne / 1000
		cervejaFinal = Math.ceil(cerveja / 355)
		bebidaFinal = Math.ceil(bebida / 2000)

		// determina se a "bebida" deve estar no plural ou não
		if (bebidaFinal > 1) {
			bebidaPlural = 's';
		} else {
			bebidaPlural = '';
		}

    // console.log(
    //   `Carne: ${carne}g; Cerveja: ${cerveja}ml; Refrigerante/Água: ${bebida}ml`
    // );

		// mostra a modal
		modal = true;
  }
</script>

<main>
  <div class="box" style="height: {boxHeight}px">
    <h1>Churrascômetro 🍖</h1>

    <div class="form-area">
      <form on:submit={calculo}>
        <input
          id="adultos"
					bind:this={inputAdultos}
          name="adultos"
          type="number"
          placeholder="Adultos"
          min={1}
          required
        />
        <input
          id="criancas"
					bind:this={inputCriancas}
          name="criancas"
          type="number"
          placeholder="Crianças"
          min={0}
          required
        />
        <input
          id="duracao"
					bind:this={inputDuracao}
          name="duracao"
          type="number"
          placeholder="Duração (horas)"
          min={1}
          required
        />
        <button type="submit" class="calc-button" style="margin-top: {buttonMarginTop}px">Calcular</button>
      </form>
    </div>
  </div>

	{#if modal}
  <div class="modal" transition:slide>
    <div class="modal-content" style="height: {boxHeight}px">
      <h1 class="modal-title" in:fade="{{duration: 250}}" out:fade="{{duration: 100}}">Você precisará de...</h1>

      <div class="text-area" in:fade="{{duration: 250}}" out:fade="{{duration: 100}}">
        <p>
          <strong>{carneFinal} kg</strong> de carne 🥩
        </p>
        <p>
          <strong>{cervejaFinal} latas</strong> de cerveja 🍺
        </p>
        <p>
          <strong>{bebidaFinal} garrafa{bebidaPlural}</strong> de 2L de
          bebidas 🥤
        </p>
      </div>

      <button class="modal-button" on:click={() => {modal = false}} in:fade="{{duration: 250}}" out:fade="{{duration: 100}}">Voltar</button>
    </div>
  </div>
	{/if}
</main>

<style>
  .modal-title {
    color: #fff;
  }

  main {
    display: flex;
    align-items: center;
    margin: 0 auto;
    width: 35%;
    height: 100vh;
  }

  h1 {
    font-family: "Roboto", sans-serif;
    font-size: 50px;
    margin: 0 auto;
    color: #000;
  }

  .box {
    display: flex;
    flex-direction: column;
    justify-content: space-between;

    margin: 0;
    padding: 5%;
    width: 100%;
    /* height: 50%; */
    border-radius: 5px;

    background-color: #fff;
    box-shadow: 0 2px 6px rgba(0, 0, 0, 0.2);
  }

  .form-area {
    margin: 0 auto;
  }

  input {
    width: 100%;
    height: 50px;
    margin: 0 0 10px;
    padding: 0 10px;
    box-sizing: border-box;

    outline: none;
    border: solid 2px #a0a0a0;
    border-radius: 3px;

    font-family: "Nunito Sans", sans-serif;
    font-size: 16px;
    color: #a0a0a0;

    transition: 200ms;
  }

  input:focus,
  input:valid {
    border: solid 2px #000 !important;
    color: #000 !important;
  }

  input:focus::placeholder,
  input:valid::placeholder {
    color: #606060 !important;
  }

  input:hover {
    border: solid 2px #606060;
    color: #606060;
  }

  input:hover::placeholder {
    color: #606060;
  }

  input::placeholder {
    font-family: "Nunito Sans", sans-serif;
    font-size: 16px;
    font-weight: 600;
    color: #a0a0a0;
  }

  .calc-button {
    width: 100%;
    height: 50px;
    /* margin: 16% 0 0; */

    outline: none;
    border: solid 2px #a0a0a0;
    border-radius: 3px;
    background-color: #fff;

    font-family: "Nunito Sans", sans-serif;
    font-size: 17px;
    font-weight: 600;
    color: #777;

    transition: 300ms;
  }

  form:valid button {
    border: solid 2px #7d3109;
    background-color: #e84757;
    color: #f8fcfd;
  }

  form:valid button:hover {
    border: solid 2px #c94e0e;
    background-color: #ff4e4e;
    color: #fff;

    cursor: pointer;
  }

  .modal {
    height: 100vh;
    width: 35%;

    position: absolute;
    left: 50%;
    top: 50%;
    -webkit-transform: translate(-50%, -50%);
    transform: translate(-50%, -50%);

    display: flex;
    align-items: center;
    justify-content: center;
    margin: 0 auto;
  }

  .modal-content {
    display: flex;
    flex-direction: column;
    justify-content: space-between;

    border-radius: 5px;
    padding: 5%;
    width: 100%;
    /* height: 50%; */

    background-color: #ff574d;
    box-shadow: 0 2px 6px rgba(0, 0, 0, 0.2);

    transition: 300ms;
  }

  .text-area {
    text-align: center;
  }

  p {
    font-family: "Nunito Sans", sans-serif;
    font-size: 30px;
    font-weight: 400;
    color: #fff;

    margin: 20px 0;
  }

  .modal-button {
    width: 100%;
    height: 50px;
    margin: 0 auto;

    background-color: #964b00;
    outline: none;
    border: solid 2px #633100;
    border-radius: 3px;

    font-family: "Nunito Sans", sans-serif;
    font-size: 18px;
    font-weight: 600;
    color: #f0f0f0;

    transition: 300ms;
  }

  .modal-button:hover {
    background-color: #bc5e00;
    border: solid 2px #894400;
    color: #f8fcfd;
    cursor: pointer;
  }

  @media (max-width: 700px) {
    main {
      width: 90%;
    }

    .box {
      width: 90%;
      margin: 0 auto;
    }

    h1 {
      font-size: 9vw;
    }

    input {
      height: 45px;
      font-size: 14px;
      padding: 3%;
    }

    input::placeholder {
      font-size: 14px;
    }

    .calc-button {
      height: 45px;
      font-size: 15px;

      margin: clamp(30px, 9vh, 70px) 0 0;
    }

		.modal {
			width: 90%;
		}

		p {
			font-size: 21px;
		}

		.modal-button {
			height: 45px;
			font-size: 15px;
		}
  }

  @media (max-height: 700px) {
    .calc-button {
      margin: 5.5vh 0 0;
    }
  }

	@media (min-width: 701px) and (orientation: portrait) {
		main {
      width: 90%;
    }

		.modal {
			width: 90%;
		}
	}

	@media (min-width: 701px) and (max-width: 1900px) and (orientation: landscape) {
		main {
			width: 40%;
		}

		.box {
			height: 60%;
		}

		.modal {
			width: 40%;
		}

		.modal-content {
			height: 60%;
		}
	}

	@media (min-width: 2000px) and (orientation: landscape) {
		h1 {
			font-size: 3vw;
		}

		input {
			height: 5vh;
			font-size: .8vw;
		}

		input::placeholder {
			font-size: .8vw;
		}

		.calc-button {
			height: 5vh;
			font-size: 1vw;
			margin: 15vh 0 0;
		}

		p {
			font-size: 1.5vw;
		}

		.modal-button {
			height: 5vh;
			font-size: 1vw;
		}
	}
</style>
