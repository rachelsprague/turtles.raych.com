---
layout: landing
title: Raych's Turtle Cove
body_class: turtles-page
---

<div class="landing-container">

  <!-- Logo / Icon -->
  <img src="/assets/images/turtle_logo.png" alt="Raych's Turtle Cove logo" class="logo">

  <!-- Social media icons inline -->
  <div class="social-icons-inline">
    <a href="https://bsky.app/profile/turtles.raych.com" target="_blank" rel="noopener" aria-label="Bluesky">
      <img src="/assets/icons/bluesky.svg" alt="Bluesky" class="social-svg">
    </a>
    <a href="https://youtube.com/@turtle_cove_raych" target="_blank" rel="noopener" aria-label="YouTube">
      <i class="fab fa-youtube"></i>
    </a>
    <a href="https://open.spotify.com/user/31ekhjd5x5qoyln7zo4zkv4tneay" target="_blank" rel="noopener" aria-label="Spotify">
      <i class="fab fa-spotify"></i>
    </a>
    <a  href="https://www.twitch.tv/raych_com" target="_blank" rel="noopener" aria-label="Twitch">
      <i class="fab fa-twitch"></i>
    </a>
  </div>

  <!-- Links Section -->
  <h2>Links</h2>
  <a href="/tanks" rel="noopener">Aquariums</a>
  <a href="https://www.twitch.tv/raych_com" target="_blank" rel="noopener">🐠 Aquarium Livestream 🐠</a>
  <a href="https://open.spotify.com/user/31ekhjd5x5qoyln7zo4zkv4tneay" target="_blank" rel="noopener">🎧 Playlists 🎧</a>
  <a class="btn btn--primary btn--large btn--block" href="https://www.raych.com" target="_blank" rel="noopener">Raych</a>
  <a class="btn btn--primary btn--large btn--block" href="http://www.raych.com/makes/" target="_blank" rel="noopener">Raych Makes</a>

  <section class="latest">
  <h2>Latest Turtle Photo</h2>
  <div id="latest-photo"></div>

  <h2>Latest Turtle Video</h2>
  <div id="latest-video"></div>
</section>

</div>


<script>
async function loadBlueskyMedia() {
  const handle = "turtles.raych.com";

  const res = await fetch(
    `https://public.api.bsky.app/xrpc/app.bsky.feed.getAuthorFeed?actor=${handle}&limit=50`
  );
  const data = await res.json();

  let photoPost = null;
  let videoPost = null;

  for (const item of data.feed) {

    // skip reposts
    if (item.reason) continue;

    const embed = item.post.embed;

    // pick first image post
    if (!photoPost && embed?.images?.length) {
      photoPost = embed.images[0];
    }

    // pick first video post
    if (!videoPost && embed?.record?.text?.includes('video')) {
      // try to find the video src in attachments
      const videoAttachment = embed?.media?.[0];
      if (videoAttachment?.type === 'video') {
        videoPost = videoAttachment;
      }
    }

    if (photoPost && videoPost) break;
  }

  // show photo with max width
  if (photoPost) {
    const url = photoPost.thumb || photoPost.fullsize; // thumb if available
    document.getElementById("latest-photo").innerHTML =
      `<img src="${url}" style="max-width:100%;border-radius:12px;">`;
  }

  // show video (if exists)
  if (videoPost) {
    const videoUrl = videoPost.url || videoPost.playlist;
    if (videoUrl) {
      document.getElementById("latest-video").innerHTML =
        `<video controls style="max-width:100%;border-radius:12px;">
           <source src="${videoUrl}" type="video/mp4">
         </video>`;
    }
  }
}

loadBlueskyMedia();
</script>
  
  <!-- Hub text -->
  <!--  <p>The turtles racked up 12.6k+ followers and 300k+ likes on TikTok—but I've moved all their content off the platform. You can now follow them on Bluesky <a href="https://bsky.app/profile/turtles.raych.com">@turtles.raych.com</a>.</p>
    <p>I have three red-eared slider turtles, all around 20 years old. I also keep two 20-gallon planted guppy tanks and share my home with a senior squad of dogs (down to one 😭😭😭).</p>
-->