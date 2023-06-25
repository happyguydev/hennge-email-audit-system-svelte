<script lang="ts">
  export let recipients: string[]

  let currentWidth: number = 0
  let badgeWidth: number = 0
  let badgeCount: number = recipients.length

  // have the array updated when currentWidth updates
  $: recipientsToShow = render(currentWidth)

  function render(currentWidth: number) {
    // Early return
    if (currentWidth == 0) return []

    let i = 0
    let sumWidth = 0
    let realWidth = currentWidth - badgeWidth - 30

    // While instead of for, removes the need for breaks
    while (i < recipients.length && sumWidth < realWidth) {
      // Create a dummy span and add it somewhere far off screen
      let span: HTMLElement = document.createElement('span')
      span.classList.add('recipient')
      span.textContent = `${recipients[i]}\xa0`
      span.setAttribute('visibility', 'hidden')
      span.setAttribute('position', 'fixed')
      span.setAttribute('top', '-1000')
      document.body.appendChild(span)

      // Sum the width of the dummy elements
      sumWidth += span.getBoundingClientRect().width

      // Remove the dummy again
      document.body.removeChild(span)

      // if the sum with the dummy is less than the current width do i + 1
      // this will effectively include this dino in the list
      if (sumWidth < realWidth) {
        i++
      }
    }

    if (i === 0) {
      badgeCount = recipients.length - 1 // Set badge coun

      // return the first value
      return recipients[0]
    } else {
      badgeCount = recipients.length - i // Set badge coun

      // return the subarray that fits.
      // note that if sumWidth > currentWidth this will NOT include the dino that was too big
      return recipients.slice(0, i).join(', ')
    }
  }
</script>

<style>
  .display {
    overflow: hidden;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  .recipient {
    display: inline-block;
    font-size: 16px;
    color: #333333;
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
  }
  .badge {
    font-size: 16px;
    color: #f0f0f0;
    background-color: #666666;
    border-radius: 3px;
    padding: 2px 5px;
    margin: 0;
  }
</style>

<div class="display" bind:clientWidth={currentWidth}>
  <span class="recipient">
    {#if badgeCount === 0}
      {recipientsToShow}
    {:else}{recipientsToShow.concat(', ...')}{/if}
  </span>

  {#if badgeCount > 0}
    <p class="badge" bind:clientWidth={badgeWidth}>+{badgeCount}</p>
  {/if}
</div>
