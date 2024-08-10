<script>
import ConversationForm from './ConversationForm.vue';

export default {
  components: {
    ConversationForm,
  },
  props: {
    show: {
      type: Boolean,
      default: false,
    },
    contact: {
      type: Object,
      default: () => ({}),
    },
  },
  watch: {
    'contact.id'(id) {
      this.$store.dispatch('contacts/fetchContactableInbox', id);
    },
  },
  mounted() {
    const { id } = this.contact;
    this.$store.dispatch('contacts/fetchContactableInbox', id);
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
        params,
        isFromWhatsApp,
      });
      return data;
    },
  },
};
</script>

<!-- eslint-disable vue/no-mutating-props -->
<template>
  <woot-modal :show.sync="show" :on-close="onCancel">
    <div class="flex flex-col h-auto overflow-auto">
      <woot-modal-header
        :header-title="$t('NEW_CONVERSATION.TITLE')"
        :header-content="$t('NEW_CONVERSATION.DESC')"
      />
      <ConversationForm
        :contact="contact"
        :on-submit="onSubmit"
        @success="onSuccess"
        @cancel="onCancel"
      />
      <label
          v-if="isOneOffType"
          class="multiselect-wrap--small"
          :class="{ error: v$.selectedAudience.$error }"
        >
          {{ $t('CAMPAIGN.ADD.FORM.AUDIENCE.LABEL') }}
          <multiselect
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
