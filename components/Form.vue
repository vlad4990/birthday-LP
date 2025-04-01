<script setup lang="ts">
  const fieldWho = ref(null);
  const errorWho = ref(null)

  const fieldWillCome = ref(null)
  const itemsWillCome = [
    'Да',
    'Нет'
  ]
  const errorWillCome = ref(null)

  const fieldDrink = ref(null)
  const itemsdrink = [
    'Лёгкое',
    'Крепкое\n(Будем пить коктейлями <span class="text-xs">но это не точно</span>)',
    'Мама, я ЗОЖ-ник'
  ]
  const errorDrink = ref(null)

  const fieldAdditionalDrink = ref(null)

  const fieldNight = ref(null)
  const itemsNight = [
      'Да',
      'Нет'
  ]
  const errorNight = ref(null)

  const isSend = ref(false)

  const fieldWishes = ref('')

  const isLoading = ref(false)

  const isError = ref(false)

  const sendCount = ref(0)

  const sendform = () => {
    isLoading.value = false
    errorWho.value = null
    errorDrink.value = null
    errorWillCome.value = null
    errorNight.value  = null

    if(!fieldWho.value){
      errorWho.value = 'Пожалуйста введи кто ты'
      document.getElementById('who-id').scrollIntoView({ behavior: 'smooth', block: 'start' })
      return
    }
    if(!fieldWillCome.value){
      errorWillCome.value = 'Обязательное поле'
      document.getElementById('will-come-id').scrollIntoView({ behavior: 'smooth', block: 'start' })
      return
    }
    if(fieldWillCome.value == 'Да' && !fieldDrink.value){
      errorDrink.value = 'Обязательное поле'
      document.getElementById('drink-id').scrollIntoView({ behavior: 'smooth', block: 'start' })
      return
    }
    if(fieldWillCome.value == 'Да' && !fieldNight.value){
      errorNight.value = 'Обязательное поле'
      document.getElementById('night-id').scrollIntoView({ behavior: 'smooth', block: 'start' })
      return
    }


    const telegramBotToken = '7214396380:AAHLg26Kc-VCCJiC4ufnbQrLHLt0p7W56_0'
    const chatId = '354977159'
    const text = `
    кто: ${fieldWho.value};
    Придет: ${fieldWillCome.value};
    что будет пить: ${fieldDrink.value} --- (${fieldAdditionalDrink.value});
    будет ночевать: ${fieldNight.value}
    пожелания: ${fieldWishes.value}`
    let url = `https://api.telegram.org/bot${telegramBotToken}/sendMessage?chat_id=${chatId}&text=${encodeURIComponent(text)}`;
    sendCount.value = Number(sendCount.value) + 1
    fetch(url).then(response => {
      isSend.value = true
    }).catch(() => {
      if(Number(sendCount.value) < 2){
        setTimeout(sendform, 500)
      } else {
        isError.value = 'что-то тупит... , нажми еще раз, или напиши Кристине'
      }
    });
  }
</script>

<template>
  <div class="form pt-40 relative">
    <NuxtImg class="absolute right-0 top-0" src="/assets/img/pank.png" format="webp" width="358" height="271" placeholder/>
    <UiSectionTitle class="h-20 flex pl-5 pt-2 text-left">
      Пожалуйста, ответь<br/>
      на вопросы.
    </UiSectionTitle>
    <form class="w-full flex flex-col p-5 gap-5">
      <UiTextField id="who-id" label="1. Кто?" v-model="fieldWho" :error-message="errorWho" @input="errorWho = null"/>
      <UiSelect id="will-come-id" label="2. Ты придёшь? " v-model="fieldWillCome" :items="itemsWillCome" :error-message="errorWillCome"  @input="errorWillCome = null"/>
      <UiSelect id="drink-id" label="3. Что ты будешь пить?" v-model="fieldDrink" :items="itemsdrink" :error-message="errorDrink"  @input="errorDrink = null">
        <template #option="{value: item}">
          <div v-html="item.label"></div>
        </template>
      </UiSelect>
      <Transition name="slide-in-up">
        <div v-if="!['Мама, я ЗОЖ-ник', null].includes(fieldDrink)" class="flex flex-col font-[Furore]">
          <div class="pl-2 flex flex-col mb-1">Напиши что именно :) <br/><span class="text-xs">(Какое вино/пиво/топливо...)</span></div>
          <textarea v-model="fieldAdditionalDrink" oninput='this.style.height = "";this.style.height = this.scrollHeight + "px"' class="outline-1 outline-black/10 -outline-offset-1 rounded-lg p-2"/>
        </div>
      </Transition>
      <UiSelect id="night-id" label="4. Останешься на ночь?" v-model="fieldNight" :items="itemsNight" :error-message="errorNight" @input="errorNight = null"/>
      <div class="flex flex-col font-[Furore]">
        <div class="pl-2 leading-[1]">4.Предложения и пожелания<br/><span class="text-xs leading-[0.5]">всё для вашего удобства</span></div>
        <textarea v-model="fieldWishes" oninput='this.style.height = "";this.style.height = this.scrollHeight + "px"' class="outline-1 outline-black/10 -outline-offset-1 rounded-lg mt-2 p-2"/>
      </div>
    </form>
    <button @click="sendform" class="max-w-[351px] w-full mx-auto h-20 flex items-center justify-center text-[40px] font-[furore] text-white bg-[#AA1515] font-normal" :disabled="isSend">{{isSend ? 'Отправлено!' : 'Отправить'}}</button>
    <div v-if="isError" class="mt-1 text-center text-[#AA1515]">{{isError}}</div>
    <div class="mx-5 mt-10 text-2xl whitespace-break-spaces">Как только мы определимся<br/>с площадкой я скину тебе ссылку на тг канал<br/>с информацией.
    </div>
<!--    <NuxtImg width="221" height="306" src="/assets/img/little-kristina.png" format="webp" loading="lazy" class="-mt-15 ml-auto"/>-->
    <img src="/assets/img/little-kristina.webp" loading="lazy" class="w-[221px] h-[306px] -mt-15 ml-auto"/>
  </div>
</template>
