<script>
import { mapGetters } from 'vuex';
import { useAlert } from 'dashboard/composables';
import LabelSelector from 'dashboard/components/widgets/LabelSelector.vue';

export default {
  components: { LabelSelector },
  props: {
    contactId: {
      type: [String, Number],
      required: true,
    },
  },

  computed: {
    savedLabels() {
      const availableContactLabels = this.$store.getters[
        'contactLabels/getContactLabels'
      ](this.contactId);
      return this.allLabels.filter(({ title }) =>
        availableContactLabels.includes(title)
      );
    },

    ...mapGetters({
      allLabels: 'labels/getLabels',
    }),
  },

  watch: {
    contactId(newContactId, prevContactId) {
      if (newContactId && newContactId !== prevContactId) {
        this.fetchLabels(newContactId);
      }
    },
  },

  mounted() {
    const { contactId } = this;
    this.fetchLabels(contactId);
  },

  methods: {
    async onUpdateLabels(selectedLabels) {
      try {
        await this.$store.dispatch('contactLabels/update', {
          contactId: this.contactId,
          labels: selectedLabels,
        });
      } catch (error) {
        useAlert(this.$t('CONTACT_PANEL.LABELS.CONTACT.ERROR'));
      }
    },

    addItem(value) {
      const result = this.savedLabels.map(item => item.title);
      result.push(value.title);
      this.onUpdateLabels(result);
    },

    removeItem(value) {
      const result = this.savedLabels
        .map(label => label.title)
        .filter(label => label !== value);
      this.onUpdateLabels(result);
    },

    async fetchLabels(contactId) {
      if (!contactId) {
        return;
      }
      this.$store.dispatch('contactLabels/get', contactId);
    },
  },
};
</script>

<template>
  <LabelSelector
    :all-labels="allLabels"
    :saved-labels="savedLabels"
    @add="addItem"
    @remove="removeItem"
  />
</template>
