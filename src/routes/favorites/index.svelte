<script>
  import { favorites } from "../../favorites";

  function handleMouseOver(e) {
    e.target.play();
  }
</script>

<style>
  .gifs {
    display: flex;
    flex-wrap: wrap;
    width: max(500px, 40em);
    margin: 0 auto;
    list-style: none;
  }

  .gif {
    margin: 10px;
  }

  .gif video {
    width: 300px;
    height: 300px;
    object-fit: cover;
  }
  h2,
  h3 {
    text-align: center;
  }
</style>

<h2>Favorties</h2>
{#if $favorites.length > 0}
  <ul class="gifs">
    {#each $favorites as favorite}
      <li class="gif">
        <video
          poster={favorite.images.original_still.url}
          src={favorite.images.looping.mp4}
          on:mouseover={handleMouseOver}>
          <track
            default
            kind="captions"
            srclang="en"
            src={favorite.images.looping.mp4} />
          Sorry, your browser doesn't support video.
        </video>
      </li>
    {/each}
  </ul>
{:else}
  <h3>No Favorites Yet...</h3>
{/if}
