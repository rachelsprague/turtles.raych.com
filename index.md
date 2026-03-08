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
  <h2>Latest Photo</h2>
  <div id="latest-photo"></div>

  <h2>Latest Video</h2>
  <div id="latest-video"></div>
</section>

</div>

<script>
async function loadBlueskyMedia() {
  const handle = "turtles.raych.com";

  try {
    const res = await fetch(
      `https://public.api.bsky.app/xrpc/app.bsky.feed.getAuthorFeed?actor=${handle}&limit=40`
    );

    const data = await res.json();

    let photoHtml = "<p>No recent photo.</p>";
    let videoHtml = "<p>No recent video.</p>";

    for (const item of data.feed) {

      if (item.reason) continue; // skip reposts

      const embed = item.post.embed;
      const postId = item.post.uri.split("/").pop();
      const postUrl = `https://bsky.app/profile/${handle}/post/${postId}`;

      // PHOTO
      if (photoHtml.includes("No recent")) {
        const images = embed?.images || embed?.media?.images;

        if (images?.length) {
          photoHtml = `
            <a href="${postUrl}" target="_blank">
              <img src="${images[0].fullsize}" style="max-width:100%;border-radius:12px;">
            </a>
          `;
        }
      }

      // VIDEO
      if (videoHtml.includes("No recent")) {
        const video = embed?.video || embed?.media?.video;

        if (video?.cid) {
          const thumb = `https://video.bsky.app/watch/${handle}/${video.cid}/thumbnail.jpg`;

          videoHtml = `
            <a href="${postUrl}" target="_blank">
              <img src="${thumb}" style="max-width:100%;border-radius:12px;">
              <div style="margin-top:6px;font-size:0.9em;">▶ Watch video</div>
            </a>
          `;
        }
      }

      if (!photoHtml.includes("No recent") && !videoHtml.includes("No recent")) break;
    }

    document.getElementById("latest-photo").innerHTML = photoHtml;
    document.getElementById("latest-video").innerHTML = videoHtml;

  } catch (err) {
    console.error("Bluesky load error:", err);
  }
}

loadBlueskyMedia();
</script>
  
  <!-- Hub text -->
  <!--  <p>The turtles racked up 12.6k+ followers and 300k+ likes on TikTok—but I've moved all their content off the platform. You can now follow them on Bluesky <a href="https://bsky.app/profile/turtles.raych.com">@turtles.raych.com</a>.</p>
    <p>I have three red-eared slider turtles, all around 20 years old. I also keep two 20-gallon planted guppy tanks and share my home with a senior squad of dogs (down to one 😭😭😭).</p>
-->