<script lang="ts">
  export let datetime: string;
  const myDatetime = new Date(datetime);

  const date = myDatetime.toLocaleDateString([], {
    year: "numeric",
    month: "long",
    day: "numeric",
  });

  const time = myDatetime.toLocaleTimeString([], {
    hour: "2-digit",
    minute: "2-digit",
  });
</script>

<div>
  {date}
  <span aria-hidden="true"> | </span>
  <span class="sr-only">&nbsp;at&nbsp;</span>
  {time}
</div>
