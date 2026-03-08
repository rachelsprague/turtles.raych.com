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
      `https://public.api.bsky.app/xrpc/app.bsky.feed.getAuthorFeed?actor=${handle}&limit=20`
    );
    const data = await res.json();

    let photoFound = false;
    let videoFound = false;

    let photoHtml = "<p>No recent photo found.</p>";
    let videoHtml = "<p>No recent video found.</p>";

    for (const item of data.feed) {
      // Skip reposts
      if (item.reason) continue;

      const embed = item.post.embed;
      const postUri = item.post.uri;
      const postUrl = `https://bsky.app/profile/${handle}/post/${postUri.split('/').pop()}`;

      // Latest photo
      if (!photoFound && embed?.images?.length) {
        const image = embed.images[0];
        photoHtml = `
          <a href="${postUrl}" target="_blank" rel="noopener">
            <img src="${image.fullsize}" style="max-width:100%;border-radius:12px;">
          </a>
        `;
        photoFound = true;
      }

      // Latest video
      if (!videoFound && embed?.video) {
        const video = embed.video;
        let videoUrl = null;

        if (video.playlist && video.playlist.length) {
          videoUrl = video.playlist[0].url;
        } else if (video.url) {
          videoUrl = video.url;
        }

        if (videoUrl) {
          videoHtml = `
            <a href="${postUrl}" target="_blank" rel="noopener">
              <video controls style="max-width:100%;border-radius:12px;">
                <source src="${videoUrl}" type="application/x-mpegURL">
                Your browser does not support the video tag.
              </video>
            </a>
          `;
          videoFound = true;
        }
      }

      if (photoFound && videoFound) break;
    }

    document.getElementById("latest-photo").innerHTML = photoHtml;
    document.getElementById("latest-video").innerHTML = videoHtml;

  } catch (err) {
    console.error("Error loading Bluesky media:", err);
    document.getElementById("latest-photo").innerHTML = "<p>Error loading photo.</p>";
    document.getElementById("latest-video").innerHTML = "<p>Error loading video.</p>";
  }
}

loadBlueskyMedia();
</script>
  
  <!-- Hub text -->
  <!--  <p>The turtles racked up 12.6k+ followers and 300k+ likes on TikTok—but I've moved all their content off the platform. You can now follow them on Bluesky <a href="https://bsky.app/profile/turtles.raych.com">@turtles.raych.com</a>.</p>
    <p>I have three red-eared slider turtles, all around 20 years old. I also keep two 20-gallon planted guppy tanks and share my home with a senior squad of dogs (down to one 😭😭😭).</p>
-->