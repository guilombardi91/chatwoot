<script>
import { formatDate } from 'shared/helpers/DateHelper';
import darkModeMixin from 'widget/mixins/darkModeMixin.js';

export default {
  mixins: [darkModeMixin],
  props: {
    date: {
      type: String,
      required: true,
    },
  },
  computed: {
    formattedDate() {
      return formatDate({
        date: this.date,
        todayText: this.$t('TODAY'),
        yesterdayText: this.$t('YESTERDAY'),
      });
    },
  },
};
</script>

<template>
  <div
    class="date--separator"
    :class="$dm('text-slate-700', 'dark:text-slate-200')"
  >
    {{ formattedDate }}
  </div>
</template>

<style lang="scss" scoped>
@import '~widget/assets/scss/variables';

.date--separator {
  font-size: $font-size-default;
  height: 50px;
  line-height: 50px;
  position: relative;
  text-align: center;
  width: 100%;
}

.date--separator::before,
.date--separator::after {
  background-color: $color-border;
  content: '';
  height: 1px;
  position: absolute;
  top: 24px;
  width: calc((100% - 120px) / 2);
}

.date--separator::before {
  left: 0;
}

.date--separator::after {
  right: 0;
}
</style>
