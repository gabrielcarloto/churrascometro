<script>
  import { fade, slide } from "svelte/transition";

  let fieldset;
  let inputAdultos;
  let inputCriancas;
  let inputDuracao;
  let carne;
  let bebida;
  let cerveja;
  let carneFinal;
  let bebidaFinal;
  let cervejaFinal;
  let bebidaPlural;
  let modal = false;
  let reduceMotion = matchMedia("(prefers-reduced-motion)").matches;

  // calcula os valores do box e da margem do botão
  // para não ter problemas ao abrir o teclado em dispositivos mobile
  const windowHeight = window.innerHeight;
  const boxHeight = windowHeight < 700 ? windowHeight * 0.6 : windowHeight / 2;
  const buttonMarginTop = windowHeight < 700 ? boxHeight / 10 : boxHeight / 5.5;

  // função para fechar a modal
  function closeModal() {
    modal = false;
    fieldset.removeAttribute("disabled", "disabled");
  }

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
    inputAdultos.value = "";
    inputCriancas.value = "";
    inputDuracao.value = "";

    // calcula a quantia de carne e bebidas com base nos dados do formulário
    /*
				CARNE -> 400 gr por pessoa                   + de 6 horas -> 650 gr
				CERVEJA -> 1200 ml por pessoa                + de 6 horas -> 2000 ml
				REFRIGERANTE/ÁGUA -> 1000 ml por pessoa      + de 6 horas -> 1500 ml
				
				crianças valem por 0,5
			*/

    function carnePorPessoa(duracao) {
      return duracao >= 6 ? 650 : 400;
    }

    function cervejaPorPessoa(duracao) {
      return duracao >= 6 ? 2000 : 1200;
    }

    function bebidaPorPessoa(duracao) {
      return duracao >= 6 ? 1500 : 1000;
    }

    carne =
      adultos * carnePorPessoa(duracao) +
      criancas * carnePorPessoa(duracao) * 0.5;
    cerveja = adultos * cervejaPorPessoa(duracao);
    bebida =
      adultos * bebidaPorPessoa(duracao) +
      criancas * bebidaPorPessoa(duracao) * 0.5;

    carneFinal = carne / 1000;
    cervejaFinal = Math.ceil(cerveja / 355);
    bebidaFinal = Math.ceil(bebida / 2000);

    // determina se a "bebida" deve estar no plural ou não
    bebidaPlural = bebidaFinal > 1 ? "s" : "";

    // mostra a modal e desabilita o formulário para que o site seja mais keyboard-friendly
    modal = true;
    fieldset.setAttribute("disabled", "disabled");

    // fecha a modal ao apertar enter
    if (modal === true) {
      document.addEventListener("keydown", (e) => {
        if (e.key === "Enter") {
          closeModal();
        }
      });
    }
  }
</script>

<main>
  <div class="box" style="height: {boxHeight}px">
    <h1>Churrascômetro 🍖</h1>

    <div class="form-area">
      <form on:submit={calculo}>
        <fieldset bind:this={fieldset}>
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
          <button
            type="submit"
            class="calc-button"
            style="margin-top: {buttonMarginTop}px">Calcular</button
          >
        </fieldset>
      </form>
    </div>
  </div>

  {#if modal}
    <div
      class="modal"
      role="dialog"
      aria-labelledby="modal-title"
      transition:slide={{ duration: reduceMotion ? 0 : 550 }}
    >
      <div class="modal-content" style="height: {boxHeight}px">
        <h1
          class="modal-title"
          id="modal-title"
          in:fade={{ duration: reduceMotion ? 0 : 250 }}
          out:fade={{ duration: reduceMotion ? 0 : 100 }}
        >
          Você precisará de...
        </h1>

        <div
          class="text-area"
          in:fade={{ duration: reduceMotion ? 0 : 250 }}
          out:fade={{ duration: reduceMotion ? 0 : 100 }}
        >
          <p>
            <strong>{carneFinal} kg</strong> de carne 🥩
          </p>
          <p>
            <strong>{cervejaFinal} latas</strong> de cerveja 🍺
          </p>
          <p>
            <strong>{bebidaFinal} garrafa{bebidaPlural}</strong> de 2L de bebida{bebidaPlural}
            🥤
          </p>
        </div>

        <button
          class="modal-button"
          on:click={() => {
            closeModal();
          }}
          in:fade={{ duration: reduceMotion ? 0 : 250 }}
          out:fade={{ duration: reduceMotion ? 0 : 100 }}>Voltar</button
        >
      </div>
    </div>
  {/if}
</main>