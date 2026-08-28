<script setup lang="ts">
import { computed, nextTick, reactive, ref, watch } from "vue";

const GENDER_MAP = {
  F: "Feminina",
  M: "Masculina"
} as const;

const GAME_TYPE_MAP = {
  S: "Simples",
  D: "Dupla"
} as const;

const BASE_CATEGORIES_MAP = {
  A: "Aberta A",
  B: "Aberta B",
  C: "Aberta C",
  Sub11: "Sub 11",
  Sub13: "Sub 13",
  Sub15: "Sub 15",
  Sub17: "Sub 17",
  Sub19: "Sub 19",
  SA: "Sênior A (35 anos ou mais)",
  SB: "Sênior B (35 anos ou mais)",
  SC: "Sênior C (35 anos ou mais)",
  VA: "Veterano A (40 anos ou mais)",
  VB: "Veterano B (40 anos ou mais)",
  "M 1": "Master 1 (50 anos ou mais)",
  "M 2": "Master 2 (60 anos ou mais)"
} as const;

function buildFormData(){
  return {
    name: "",
    birthYear: null as number | null,
    club: "",
    gender: "" as "M" | "F",
    singleModeCategory: "",
    doublePartner: {
      name: "",
      birthYear: null as number | null,
      club: "",
      category: ""
    },
    mixedDoublePartner: {
      name: "",
      birthYear: null as number | null,
      club: "",
      category: ""
    }
  };
}

const formData = ref(buildFormData());
watch(() => formData.value.gender, (nv, ov) => {
  formData.value.singleModeCategory = formData.value.singleModeCategory.replace(ov, nv);
  formData.value.doublePartner.category = formData.value.doublePartner.category.replace(ov, nv);
});

const playModes = reactive({
  single: false,
  double: false,
  mixedDouble: false
})

const enum FORM_SUB_STATE {
  EDIT = 0,
  SUBMITTING,
  SUCCESS
};

const formSubmissionState = ref(FORM_SUB_STATE.EDIT);
const submissionError = ref(false);

const disableSubmission = computed(() => 
  !(playModes.single || playModes.double || playModes.mixedDouble)
);

async function submitForm(){
  submissionError.value = false;
  formSubmissionState.value = FORM_SUB_STATE.SUBMITTING;

  await nextTick();
  window.scrollTo({
    top: 0,
    behavior: "smooth"
  });

  const subData = { ...formData.value } as Partial<typeof formData.value>
  if (!playModes.single) delete subData.singleModeCategory;
  if (!playModes.double) delete subData.doublePartner;
  if (!playModes.mixedDouble) delete subData.mixedDoublePartner;

  try{
    const res = await fetch(
      "https://script.google.com/macros/s/AKfycbysT4mxrfGoWuOISkXy-s3F0fcY5ifnbRmuYNqu8jEnagOiHyea5IkR5ZSJn3LKjCc7/exec",
      {
        method: "POST",
        body: JSON.stringify(subData)
      }
    );
    if (!res.ok) throw new Error("Submission error");

    const resp = await res.json();
    if (resp.status === "error") throw new Error("Submission error");

    formSubmissionState.value = FORM_SUB_STATE.SUCCESS;

    await nextTick();
    window.scrollTo({
      top: 0,
      behavior: "smooth"
    });
  }
  catch (_e){
    submissionError.value = true;
    formSubmissionState.value = FORM_SUB_STATE.EDIT;

    await nextTick();
    window.scrollTo({
      top: document.documentElement.scrollHeight,
      behavior: "smooth"
    });
  }
}
</script>

<template>
  <header>
    <nav class="navbar bg-primary">
      <div class="container-fluid">
        <a class="navbar-brand text-bg-primary mx-auto d-flex flex-row align-items-center" href="#">
          <img src="@/assets/logo_sbb.png" alt="Logotipo SBB"
            class="logo d-inline-block align-text-top me-3"
          >
          <div class="text-wrap">
            Copa São Bernardo de Badminton 2026
          </div>
        </a>
      </div>
    </nav>
  </header>

  <main>
    <div class="container py-4">
      <div class="row">
        <div class="d-none d-md-block col-12 col-md-3"></div>
        <div class="col-12 col-md-6">
          <div class="text-center my-5">
            <h1>Formulário de inscrição | Copa São Bernardo de Badminton 2026</h1>
          </div>

          <form v-if="formSubmissionState === FORM_SUB_STATE.EDIT" @submit.prevent="submitForm">
            <div class="mb-4">
              <div>
                <div>
                  <b>Data:</b> 26 e 27 de setembro de 2026 (inscrições até 12/09/2026).
                </div>
                <div>
                  <ul>
                    <li>Dia 26: Sêniors, Veteranos e Masters;</li>
                    <li>Dia 27: Jovens e Aberta.</li>
                  </ul>
                </div>
              </div>
              <div>
                <b>Local:</b> Av. Presidente Arthur Bernardes, 55, Rudge Ramos - São Bernardo do Campo - SP.
              </div>
              <div class="mt-2">
                <div class="mb-2">
                  <b>Valores por modalidade:</b>
                </div>
                <div>
                  <ul>
                    <li>Simpres: R$ 70,00;</li>
                    <li>Duplas: R$ 70,00 (deverá ser dividido em R$35,00 por integrante).</li>
                  </ul>
                </div>
                <div>
                  O pagamento deverá ser realizado para a chave PIX do SBB (CNPJ): <b>30.380.122/0001-11</b> e o comprovante
                  enviado para o e-mail <a href="mailto:sbb@terra.com.br">sbb@terra.com.br</a>.
                </div>
              </div>
              <div class="text-center my-5">
                <div class="h5">
                  Inscrição em  lote
                </div>
                <div>
                  Para realizar a inscrição de vários atletas simultaneamente, basta baixar a seguinte planilha
                  e enviá-la devidamente prenchida junto ao comprovante de pagamento para o e-mail
                  <a href="mailto:sbb@terra.com.br">sbb@terra.com.br</a>.
                </div>
                <div class="text-center mt-3">
                  <a
                    class="btn btn-primary mx-2" target="_blank"
                    href="https://github.com/mauromascarenhas/copa-sbc-sbb/raw/refs/heads/main/misc/2026/Ficha%20de%20Inscri%C3%A7%C3%B5es%20-%20Copa%20S%C3%A3o%20Bernardo%202026.xls"
                  >
                    <i class="bi bi-file-earmark-excel"></i> Baixar planilha para inscrição em lote (Excel)
                  </a>
                </div>
              </div>
              <div class="text-center my-5">
                <div class="h5">
                  Gostaria de mais detalhes?
                </div>
                <div class="d-flex justify-content-center">
                  <a
                    class="btn btn-primary mx-2" target="_blank"
                    href="https://github.com/mauromascarenhas/copa-sbc-sbb/raw/refs/heads/main/misc/2026/Carta Convite - Copa São Bernardo de Badminton 2026.pdf"
                  >
                    <i class="bi bi-file-earmark-pdf"></i> Visualizar Carta Convite
                  </a>
                  <a
                    class="btn btn-primary mx-2" target="_blank"
                    href="https://wa.me/+5511977399090"
                  >
                    <i class="bi bi-whatsapp"></i> Falar com Fátima (WhatsApp)
                  </a>
                </div>
              </div>
            </div>

            <div class="mb-3">
              <label for="name" class="form-label">Nome completo</label>
              <div class="input-group">
                <span class="input-group-text" aria-hidden="true"><i class="bi bi-person"></i></span>
                <input v-model="formData.name" type="text" name="name" class="form-control" placeholder="Seu Nome Completo..." required>
              </div>
            </div>
            <div class="mb-3">
              <label for="year" class="form-label">Ano de nascimento</label>
              <div class="input-group">
                <span class="input-group-text" aria-hidden="true"><i class="bi bi-calendar"></i></span>
                <input v-model="formData.birthYear" type="number" name="year" class="form-control" min="1900" max="2026" placeholder="1900" required>
              </div>
            </div>
            <div class="mb-5">
              <label for="club" class="form-label">Clube ao qual pertence (ou que representará no torneio)</label>
              <div class="input-group">
                <span class="input-group-text" aria-hidden="true"><i class="bi bi-person-lines-fill"></i></span>
                <input v-model="formData.club" type="text" name="club" class="form-control" placeholder="Ex.: SBB - São Bernardo Badminton" required>
              </div>
            </div>

            <div class="mt-3 mb-3">
              <label for="gender" class="form-label">Sexo (apenas para fins de escolha de modalidade da dupla)</label>
              <div class="input-group">
                <span class="input-group-text" aria-hidden="true"><i class="bi bi-gender-ambiguous"></i></span>
                <select v-model="formData.gender" class="form-select" name="gender" required>
                  <option value="" selected disabled>Selecionar...</option>
                  <option value="F">Feminino</option>
                  <option value="M">Masculino</option>
                </select>
              </div>
            </div>

            <div v-if="!!formData.gender" class="mb-4">
              <div class="form-label mb-2">Selecione as modalidades que pretende jogar (no máximo duas):</div>
              <div class="form-check">
                <input v-model="playModes.single" class="form-check-input" type="checkbox" id="cSingle"
                  :disabled="playModes.double && playModes.mixedDouble"
                >
                <label class="form-check-label" for="cSingle">
                  Simples
                </label>
              </div>
              <div class="form-check">
                <input v-model="playModes.double" class="form-check-input" type="checkbox" id="cDouble"
                  :disabled="playModes.single && playModes.mixedDouble"
                >
                <label class="form-check-label" for="cDouble">
                  Dupla {{ GENDER_MAP[formData.gender] }}
                </label>
              </div>
              <div class="form-check">
                <input v-model="playModes.mixedDouble" class="form-check-input" type="checkbox" id="cMixDouble"
                  :disabled="playModes.single && playModes.double"
                >
                <label class="form-check-label" for="cMixDouble">
                  Dupla Mista
                </label>
              </div>
            </div>

            <template v-if="playModes.single">
              <hr />
              
              <div class="mt-5 mb-3">
                <label for="singleMode" class="form-label">Categoria para a modalidade "Simples"</label>
                <div class="input-group">
                  <span class="input-group-text" aria-hidden="true"><i class="bi bi-person-arms-up"></i></span>
                  <select v-model="formData.singleModeCategory" class="form-select" name="singleMode" required>
                    <option value="" selected disabled>Selecionar...</option>
                    <option v-for="v, k in BASE_CATEGORIES_MAP" :key="k" :value="'S' + formData.gender + k">
                      {{ `S${formData.gender}${k} - ${GAME_TYPE_MAP.S} ${GENDER_MAP[formData.gender]} ${v}` }}
                    </option>
                  </select>
                </div>
              </div>
            </template>

            <template v-if="playModes.double">
              <hr />

              <div class="mt-5 mb-3">
                <label for="doubleMode" class="form-label">Categoria para a modalidade de "Dupla {{ GENDER_MAP[formData.gender] }}"</label>
                <div class="input-group">
                  <span class="input-group-text" aria-hidden="true"><i class="bi bi-people-fill"></i></span>
                  <select v-model="formData.doublePartner.category" class="form-select" name="doubleMode" required>
                    <option value="" selected disabled>Selecionar...</option>
                    <option v-for="v, k in BASE_CATEGORIES_MAP" :key="k" :value="'D' + formData.gender + k">
                      {{ `D${formData.gender}${k} - ${GAME_TYPE_MAP.D} ${GENDER_MAP[formData.gender]} ${v}` }}
                    </option>
                  </select>
                </div>
              </div>

              <div class="mb-3">
                <label for="nameDouble" class="form-label">Nome completo da dupla</label>
                <div class="input-group">
                  <span class="input-group-text" aria-hidden="true"><i class="bi bi-person"></i></span>
                  <input v-model="formData.doublePartner.name" type="text" name="nameDouble" class="form-control" placeholder="Nome Completo Da Dupla..." required>
                </div>
              </div>
              <div class="mb-3">
                <label for="yearDouble" class="form-label">Ano de nascimento da dupla</label>
                <div class="input-group">
                  <span class="input-group-text" aria-hidden="true"><i class="bi bi-calendar"></i></span>
                  <input v-model="formData.doublePartner.birthYear" type="number" name="yearDouble" class="form-control" min="1900" max="2026" placeholder="1900" required>
                </div>
              </div>
              <div class="mb-5">
                <label for="clubDouble" class="form-label">Clube ao qual a dupla pertence (ou que representará no torneio)</label>
                <div class="input-group">
                  <span class="input-group-text" aria-hidden="true"><i class="bi bi-person-lines-fill"></i></span>
                  <input v-model="formData.doublePartner.club" type="text" name="clubDouble" class="form-control" placeholder="Ex.: SBB - São Bernardo Badminton" required>
                </div>
              </div>
            </template>

            <template v-if="playModes.mixedDouble">
              <hr />

              <div class="mt-5 mb-3">
                <label for="doubleMixMode" class="form-label">Categoria para a modalidade de "Dupla Mista"</label>
                <div class="input-group">
                  <span class="input-group-text" aria-hidden="true"><i class="bi bi-people-fill"></i></span>
                  <select v-model="formData.mixedDoublePartner.category" class="form-select" name="doubleMixMode" required>
                    <option value="" selected disabled>Selecionar...</option>
                    <option v-for="v, k in BASE_CATEGORIES_MAP" :key="k" :value="'DX' + k">
                      {{ `DX${k} - ${GAME_TYPE_MAP.D} Mista ${v}` }}
                    </option>
                  </select>
                </div>
              </div>

              <div class="mb-3">
                <label for="nameMixDouble" class="form-label">Nome completo da dupla</label>
                <div class="input-group">
                  <span class="input-group-text" aria-hidden="true"><i class="bi bi-person"></i></span>
                  <input v-model="formData.mixedDoublePartner.name" type="text" name="nameMixDouble" class="form-control" placeholder="Nome Completo Da Dupla..." required>
                </div>
              </div>
              <div class="mb-3">
                <label for="yearMixDouble" class="form-label">Ano de nascimento da dupla</label>
                <div class="input-group">
                  <span class="input-group-text" aria-hidden="true"><i class="bi bi-calendar"></i></span>
                  <input v-model="formData.mixedDoublePartner.birthYear" type="number" name="yearMixDouble" class="form-control" min="1900" max="2026" placeholder="1900" required>
                </div>
              </div>
              <div class="mb-5">
                <label for="clubMixDouble" class="form-label">Clube ao qual a dupla pertence (ou que representará no torneio)</label>
                <div class="input-group">
                  <span class="input-group-text" aria-hidden="true"><i class="bi bi-person-lines-fill"></i></span>
                  <input v-model="formData.mixedDoublePartner.club" type="text" name="clubMixDouble" class="form-control" placeholder="Ex.: SBB - São Bernardo Badminton" required>
                </div>
              </div>
            </template>

            <div v-if="submissionError" class="alert alert-warning" role="alert">
              Falha ao submeter formulário. Por favor, verifique as informações preenchidas e tente novamente.
            </div>

            <button type="submit" class="btn btn-primary btn-large" :disabled="disableSubmission">Enviar</button>
          </form>


          <div v-else-if="formSubmissionState === FORM_SUB_STATE.SUBMITTING">
            <div class="text-center">
              <div class="my-4">
                <div class="spinner-border text-primary" style="width: 4rem; height: 4rem;"></div>
              </div>
              <p class="my-4 fs-3" role="status">Submetendo formulário...</p>
            </div>
          </div>

          <div v-else class="text-center">
              <div class="text-success" style="font-size: 7.5rem;">
                <i class="bi bi-check-circle-fill"></i>
              </div>

              <p class="fs-5">Formulário submetido com sucesso!</p>
              <p class="fs-5">Não se esqueça de enviar o comprovante de pagamento para <a href="mailto:sbb@terra.com">sbb@terra.com</a>!</p>
              <p class="fs-5">Estamos te esperando!</p>
          </div>
        </div>
        <div class="d-none d-md-block col-12 col-md-3"></div>
      </div>
    </div>
  </main>

  <footer class="text-bg-primary text-center py-3">
    <div><b>SBB Clube</b></div>
    <div>Avenida Presidente Arthur Bernardes, 55, Rudge Ramos - São Bernardo do Campo - SP.</div>
  </footer>
</template>

<style lang="scss">
$primary: #263089;

@import "bootstrap";
@import "bootstrap-icons/font/bootstrap-icons.css";

.logo {
  width: 2rem;
  height: 2rem;
}

main {
  min-height: calc(100vh - 8.7rem);
}
</style>
