<script lang="ts">
import type { PropType } from 'vue';
import type IReceita from '@/interfaces/IReceita.ts';
import { obterReceitas }  from '@/http/index.ts';
import BotaoPrincipal from './BotaoPrincipal.vue';
import CardCategoria from './CardCategoria.vue';
import CardReceita from './CardReceita.vue';
import { itensDeLista1EstaoEmLista2 } from '@/operacoes/listas';
export default {
    props: {
      ingredientes: { type: Array as PropType<string[]>, required: true }
    },
    data(){
        return {
            receitasEncontradas: [] as IReceita[]
        };
    },
    async created() {
        const receitas = await obterReceitas();

        //atribuição estatica: pegando as oito primeiras receitas e atribuindo a esse estado de receitas encontradas
        //receitas.slice(0, 8);
        this.receitasEncontradas = receitas.filter((receita) => {
          const possoFazerReceita = itensDeLista1EstaoEmLista2(receita.ingredientes, this.ingredientes);

          return possoFazerReceita
        })
    },
    components: { BotaoPrincipal, CardReceita },
    emits: ['editarReceitas']
}
</script>

<template>
    <section class="mostrar-receitas">
        <h1 class="cabecalho titulo-receitas">Receitas</h1>

        <p class="paragrafo-lg resultados-encontrados">
            Resultados encontrados: {{ receitasEncontradas.length }}
        </p>

        <div v-if="receitasEncontradas.length" class="receitas-wrapper">
            <p class="paragrafo-lg informacoes">
                Veja as opções de receitas que encontramos com os ingredientes que você tem por aí!!
            </p>

            <ul class="receitas">
                <li v-for="receita of receitasEncontradas" :key="receita.nome">
                    <CardReceita :receita="receita" />
                </li>
            </ul>
        </div>

        <div v-else class="receitas-nao-encontradas">
            <p class="paragrafo-lg receitas-nao-encontradas__info">
                Ops, não encontramos resultados para sua combinação. Vamos tentar de novo?
            </p>

            <img src="../assets/imagens/sem-receitas.png" 
            alt="Desenho de um novo quebrado. A gema tem um rosto com uma expressão triste">
        </div>  

        <BotaoPrincipal texto="Editar lista" @click="$emit('editarReceitas')"/>

    </section>
</template>
<style scoped>
.mostrar-receitas {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}

.titulo-receitas {
  color: var(--verde-medio, #3D6D4A);
  margin-bottom: 1.5rem;
}

.resultados-encontrados {
  color: var(--verde-medio, #3D6D4A);
  margin-bottom: 0.5rem;
}

.receitas-wrapper {
  margin-bottom: 3.5rem;
}

.informacoes {
  margin-bottom: 2rem;
}

.receitas {
  display: flex;
  justify-content: center;
  gap: 1.5rem;
  flex-wrap: wrap;
}

.receitas-nao-encontradas {
  margin-bottom: 2rem;
}

.receitas-nao-encontradas__info {
  margin-bottom: 0.5rem;
}

@media only screen and (max-width: 767px) {
  .receitas-wrapper {
    margin-bottom: 2rem;
  }
}
</style>


