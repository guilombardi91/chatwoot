<script>
import ConversationForm from './ConversationForm.vue';
import Multiselect from 'vue-multiselect';
import { mapGetters } from 'vuex';
import { useVuelidate } from '@vuelidate/core';
import { required } from '@vuelidate/validators';

export default {
  components: {
    ConversationForm,
    Multiselect,
  },
  props: {
    show: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      selectedAudience: null,
    };
  },
  validations() {
    return {
      selectedAudience: { required },
    };
  },
  computed: {
    ...mapGetters({
      audienceList: 'audiences/getAudienceList',
    }),
  },
  watch: {
    selectedAudience(newAudience) {
      // Implement any logic when selectedAudience changes
    },
  },
  mounted() {
    this.$store.dispatch('audiences/fetchAudiences');
  },
  methods: {
    onCancel() {
      this.$emit('cancel');
    },
    onSuccess() {
      this.$emit('cancel');
    },
    async onSubmit(params, isFromWhatsApp) {
      const data = await this.$store.dispatch('contactConversations/create', {
        params: { ...params, audience: this.selectedAudience },
        isFromWhatsApp,
      });
      return data;
    },
  },
  setup() {
    const v$ = useVuelidate();
    return { v$ };
  },
};
</script>

<template>
  <woot-modal :show.sync="show" :on-close="onCancel">
    <div class="flex flex-col h-auto overflow-auto">
      <woot-modal-header
        :header-title="$t('NEW_CONVERSATION.TITLE')"
        :header-content="$t('NEW_CONVERSATION.DESC')"
      />
      <ConversationForm
        :on-submit="onSubmit"
        @success="onSuccess"
        @cancel="onCancel"
      />
      <label
        class="multiselect-wrap--small"
        :class="{ error: v$.selectedAudience.$error }"
      >
        {{ $t('CAMPAIGN.ADD.FORM.AUDIENCE.LABEL') }}
        <Multiselect
          v-model="selectedAudience"
          :options="audienceList"
          track-by="id"
          label="title"
          multiple
          :close-on-select="false"
          :clear-on-select="false"
          hide-selected
          :placeholder="$t('CAMPAIGN.ADD.FORM.AUDIENCE.PLACEHOLDER')"
          selected-label
          :select-label="$t('FORMS.MULTISELECT.ENTER_TO_SELECT')"
          :deselect-label="$t('FORMS.MULTISELECT.ENTER_TO_REMOVE')"
          @blur="v$.selectedAudience.$touch"
          @select="v$.selectedAudience.$touch"
        />
        <span v-if="v$.selectedAudience.$error" class="message">
          {{ $t('CAMPAIGN.ADD.FORM.AUDIENCE.ERROR') }}
        </span>
      </label>
    </div>
  </woot-modal>
</template>

<style scoped>
.multiselect-wrap--small {
  margin-bottom: 10px;
}
.message {
  color: red;
  font-size: 0.8em;
}
</style>
