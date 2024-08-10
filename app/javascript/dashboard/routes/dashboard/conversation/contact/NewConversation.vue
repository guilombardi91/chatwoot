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
    contacts: {
      type: Array,
      default: () => [],
    },
    audiencelist: {
      type: Array,
      default: () => [],
    },
  },
  watch: {
    contacts: {
      handler(newContacts) {
        newContacts.forEach(contact => {
          this.$store.dispatch('contacts/fetchContactableInbox', contact.id);
        });
      },
      deep: true,
    },
  },
  mounted() {
    this.contacts.forEach(contact => {
      this.$store.dispatch('contacts/fetchContactableInbox', contact.id);
    });
  },
  methods: {
    onCancel() {
      this.$emit('cancel');
    },
    onSuccess() {
      this.$emit('cancel');
    },
    async onSubmit(params, isFromWhatsApp) {
      const sendMessages = this.contacts.length ? this.contacts : this.audiencelist;
      const promises = sendMessages.map(contact => {
        return this.$store.dispatch('contactConversations/create', {
          params: { ...params, contactId: contact.id },
          isFromWhatsApp,
        });
      });
      const data = await Promise.all(promises);
      return data;
    },
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
        :contacts="contacts"
        :audiencelist="audiencelist"
        :on-submit="onSubmit"
        @success="onSuccess"
        @cancel="onCancel"
      />
    </div>
  </woot-modal>
</template>
