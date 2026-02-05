<script setup lang="ts">
import { reactive, ref, computed, onMounted, onUnmounted } from "vue";
import logo from "./assets/logo.svg";

type Etapa = 1 | 2 | 3;
const etapa = ref<Etapa>(1);

const loading = ref(false);

/* ===== Dados iniciais ===== */
const dadosPessoais = reactive({
  nome: "",
  email: "",
  whatsapp: "",
  experienciaVendas: "",
});

/* ===== Questionário ===== */
const form = reactive({
  copiar: "",
  colar: "",
  navegador: "",
  pdf: "",
  enderecoSite: "",
  aba: "",
  novaAba: "",
  atendimento: "",
  ouvirCliente: "",
  convencer: "",
  posVenda: "",
  clarezaTelefone: "",
  enquantoClienteFala: "",
  duvidasCliente: "",
  clienteNaoQuerComprar: "",
});

type Pergunta = {
  key: keyof typeof form;
  titulo: string;
  opcoes: string[];
};

const perguntas: Pergunta[] = [
  {
    key: "copiar",
    titulo: "Qual atalho é usado para copiar textos?",
    opcoes: ["Ctrl + C", "Ctrl + V", "Ctrl + Z", "Shift + C"],
  },
  {
    key: "colar",
    titulo: "Qual atalho é usado para colar textos?",
    opcoes: ["Ctrl + C", "Ctrl + V", "Ctrl + Z", "Shift + V"],
  },
  {
    key: "navegador",
    titulo: "Qual programa é usado para navegar na internet?",
    opcoes: ["Word", "Excel", "Navegador (Chrome, Edge, Firefox)", "Paint"],
  },
  {
    key: "pdf",
    titulo: "O que é um arquivo PDF?",
    opcoes: [
      "Um tipo de vírus",
      "Um formato de documento",
      "Um programa do Windows",
      "Um navegador de internet",
    ],
  },
  {
    key: "enderecoSite",
    titulo: "Onde digitamos o endereço de um site?",
    opcoes: [
      "Na área de trabalho",
      "Na barra de endereços",
      "No teclado",
      "No rodapé da tela",
    ],
  },
  {
    key: "aba",
    titulo: "O que é uma aba do navegador?",
    opcoes: [
      "Um site aberto dentro do navegador",
      "Um arquivo do computador",
      "Um programa diferente",
      "Um vírus",
    ],
  },
  {
    key: "novaAba",
    titulo: "Como abrir uma nova aba no navegador?",
    opcoes: [
      "Botão + ao lado das abas",
      "Ctrl + T",
      "Clique direito → Nova aba",
      "Todas as alternativas",
    ],
  },
  {
    key: "atendimento",
    titulo: "O que é um bom atendimento ao cliente?",
    opcoes: [
      "Ignorar dúvidas",
      "Ser educado e atencioso",
      "Responder quando der vontade",
      "Falar pouco",
    ],
  },
  {
    key: "ouvirCliente",
    titulo: "Por que é importante ouvir o cliente?",
    opcoes: [
      "Terminar rápido",
      "Entender o que ele precisa",
      "Para discordar",
      "Vender qualquer coisa",
    ],
  },
  {
    key: "convencer",
    titulo: "O que ajuda a convencer o cliente a comprar?",
    opcoes: [
      "Ser curto e objetivo",
      "Responder apenas o que perguntar",
      "Explicar bem o produto",
      "Encher de perguntas",
    ],
  },
  {
    key: "posVenda",
    titulo: "O que fazer após concluir uma venda?",
    opcoes: [
      "Ignorar o cliente",
      "Agradecer e se colocar à disposição",
      "Apagar a conversa",
    ],
  },
  {
    key: "clarezaTelefone",
    titulo: "Por que é importante falar com clareza ao telefone?",
    opcoes: [
      "Parecer sério",
      "Para o cliente entender",
      "Terminar rápido",
      "Não é importante",
    ],
  },
  {
    key: "enquantoClienteFala",
    titulo: "O que o vendedor deve fazer enquanto o cliente fala?",
    opcoes: [
      "Interromper",
      "Ignorar",
      "Ouvir com atenção",
      "Falar ao mesmo tempo",
    ],
  },
  {
    key: "duvidasCliente",
    titulo: "O que fazer quando o cliente tem dúvidas?",
    opcoes: [
      "Desligar",
      "Responder com paciência",
      "Dizer que não sabe",
      "Mudar de assunto",
    ],
  },
  {
    key: "clienteNaoQuerComprar",
    titulo:
        "Se o cliente não quiser comprar naquele momento, o vendedor deve:",
    opcoes: [
      "Insistir sem parar",
      "Desligar na cara",
      "Respeitar, agradecer e enviar informações",
      "Agradecer a atenção",
    ],
  },
];

const gabarito: Record<string, string> = {
  copiar: "Ctrl + C",
  colar: "Ctrl + V",
  navegador: "Navegador (Chrome, Edge, Firefox)",
  pdf: "Um formato de documento",
  enderecoSite: "Na barra de endereços",
  aba: "Um site aberto dentro do navegador",
  novaAba: "Todas as alternativas",
  atendimento: "Ser educado e atencioso",
  ouvirCliente: "Entender o que ele precisa",
  convencer: "Explicar bem o produto",
  posVenda: "Agradecer e se colocar à disposição",
  clarezaTelefone: "Para o cliente entender",
  enquantoClienteFala: "Ouvir com atenção",
  duvidasCliente: "Responder com paciência",
  clienteNaoQuerComprar: "Respeitar, agradecer e enviar informações",
};

const pontuacao = computed(() => {
  let pontos = 0;

  for (const key in gabarito) {
    if (form[key as keyof typeof form] === gabarito[key]) {
      pontos++;
    }
  }

  return pontos;
});

/* ===== Validações ===== */
const dadosPessoaisCompletos = computed(() =>
    Object.values(dadosPessoais).every((v) => v !== "")
);

const formularioCompleto = computed(() =>
    Object.values(form).every((v) => v !== "")
);

/* ===== Controle de aba ===== */
const vezesSaiuDaAba = ref(0);

const handleVisibilityChange = () => {
  if (document.visibilityState === "hidden") {
    vezesSaiuDaAba.value++;
  }
};

onMounted(() =>
    document.addEventListener("visibilitychange", handleVisibilityChange)
);
onUnmounted(() =>
    document.removeEventListener("visibilitychange", handleVisibilityChange)
);

/* ===== Ações ===== */
const avancarParaQuestionario = () => {
  if (!dadosPessoaisCompletos.value) return;
  etapa.value = 2;
};

const submit = async () => {
  if (!formularioCompleto.value) return;

  loading.value = true;

  const payload = {
    dadosPessoais,
    respostas: form,
    pontuacao: pontuacao.value,
    saiuDaAba: vezesSaiuDaAba.value,
  };

  try {
    await fetch(
        "https://script.google.com/macros/s/AKfycby4U2joD4QaSWf-Koag_yxVeZZaj3j8Lv4iOmY5NXXmXnucClRGuWQkXebe6FHYLypoFA/exec",
        {
          method: "POST",
          body: JSON.stringify(payload),
        }
    );

    etapa.value = 3; // 🔥 sucesso
  } catch (e) {
    alert("Erro ao enviar o formulário. Tente novamente.");
  } finally {
    loading.value = false;
  }
};
</script>

<template>
  <div class="flex items-center justify-center min-h-screen p-4">
    <Card class="w-full max-w-3xl">

      <template #title>
        <div class="flex flex-col items-center rounded-2xl p-10">
          <img :src="logo" alt="Logo" class="max-h-10 md:max-h-20 dark:invert dark:brightness-10 dark:drop-shadow-lg"/>
        </div>
        <h1 class="text-xl font-semibold text-center">
          {{ etapa === 1 ? "Dados do Candidato" : "Questionário de Conhecimentos" }}
        </h1>
      </template>

      <template #content>
        <!-- ===== LOADING ===== -->
        <div v-if="loading" class="flex flex-col items-center py-16 gap-4">
          <ProgressSpinner />
          <p class="text-lg font-medium">
            Enviando suas respostas...
          </p>
        </div>
        <!-- ===== SUCESSO ===== -->
        <div v-else-if="etapa === 3" class="text-center space-y-6 py-12">

          <div class="text-4xl">✅</div>

          <h2 class="text-2xl font-semibold">
            Formulário enviado com sucesso!
          </h2>

          <p class="text-gray-600">
            Recebemos suas respostas.
            Para acelerar o processo, fale agora com o RH no WhatsApp.
          </p>

          <a
              href="https://wa.me/5548988408362"
              target="_blank"
              class="inline-flex items-center gap-2 bg-green-600 hover:bg-green-700 text-white px-6 py-3 rounded-xl font-medium transition"
          >
            💬 Falar com o RH no WhatsApp
          </a>

        </div>
        <!-- ===== ETAPA 1 ===== -->
        <form v-if="etapa === 1 && !loading" class="space-y-4">

          <div>
            <label class="font-medium">Nome completo <span class="required">*</span></label>
            <InputText v-model="dadosPessoais.nome" class="w-full" />
          </div>

          <div>
            <label class="font-medium">Email <span class="required">*</span></label>
            <InputText v-model="dadosPessoais.email" type="email" class="w-full" />
          </div>

          <div>
            <label class="font-medium">WhatsApp <span class="required">*</span></label>
            <InputMask mask="(99) 99999-9999" v-model="dadosPessoais.whatsapp" class="w-full" />
          </div>

          <div>
            <label class="font-medium">
              Possui experiência em vendas? <span class="required">*</span>
            </label>

            <div class="flex gap-4 mt-2">
              <label class="flex items-center gap-2">
                <RadioButton value="Sim" v-model="dadosPessoais.experienciaVendas" />
                Sim
              </label>
              <label class="flex items-center gap-2">
                <RadioButton value="Não" v-model="dadosPessoais.experienciaVendas" />
                Não
              </label>
            </div>
          </div>

          <div class="flex justify-end pt-4">
            <Button
                label="Iniciar questionário"
                :disabled="!dadosPessoaisCompletos"
                @click="avancarParaQuestionario"
            />
          </div>
        </form>

        <!-- ===== ETAPA 2 ===== -->
        <form v-else-if="etapa === 2 && !loading" class="space-y-6" @submit.prevent="submit">

          <div
              v-for="(pergunta, index) in perguntas"
              :key="pergunta.key"
              class="space-y-2"
          >
            <p class="font-medium">
              {{ index + 1 }}. {{ pergunta.titulo }}
              <span class="required">*</span>
            </p>

            <div class="space-y-2 pl-2">
              <label
                  v-for="opcao in pergunta.opcoes"
                  :key="opcao"
                  class="flex items-center gap-2"
              >
                <RadioButton
                    :value="opcao"
                    v-model="form[pergunta.key]"
                />
                {{ opcao }}
              </label>
            </div>
          </div>

          <div class="pt-6 flex justify-end">
            <Button
                label="Enviar formulário"
                type="submit"
                :disabled="!formularioCompleto"
            />
          </div>
        </form>
      </template>
    </Card>
  </div>
</template>

<style scoped>
.required {
  color: #dc2626;
  font-weight: 600;
}
</style>
