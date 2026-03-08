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
      `https://public.api.bsky.app/xrpc/app.bsky.feed.getAuthorFeed?actor=${handle}&filter=posts_with_media&limit=50`
    );

    const data = await res.json();

    let latestPhoto = null;
    let latestVideo = null;

    for (const item of data.feed) {
      const post = item.post;
      const embed = post.embed;

      // Skip reposts – check that reason exists and type matches exactly
      if (post.reason && post.reason.$type === "app.bsky.feed.defs#reasonRepost") {
        continue;
      }

      // PHOTO: posts with image embed
      if (!latestPhoto && embed && embed.$type === "app.bsky.embed.images#view" && embed.images?.length > 0) {
        latestPhoto = {
          url: embed.images[0].fullsize || embed.images[0].thumb,
          // post.uri is like "at://did/app.bsky.feed.post/3kxxxxx", we only want the rkey (last segment)
          rkey: post.uri.split("/").pop()
        };
      }

      // VIDEO: Bluesky videos currently come as external or record embeds with a blob URL,
      // so check for an external or record embed and look for a .mp4-like URL.
      if (!latestVideo && embed) {
        // external card with a video URL
        if (embed.$type === "app.bsky.embed.external#view" && embed.external?.uri) {
          const url = embed.external.uri;
          if (url.match(/\.(mp4|mov|webm|mpeg)(\?|$)/i)) {
            latestVideo = {
              url,
              rkey: post.uri.split("/").pop(),
              thumb: embed.external.thumb?.fullsize || embed.external.thumb?.thumb || ""
            };
          }
        }

        // record-with-media case (quote + media)
        if (!latestVideo && embed.$type === "app.bsky.embed.recordWithMedia#view") {
          const media = embed.media;
          // media can itself be an images or external embed etc.
          if (media?.$type === "app.bsky.embed.external#view" && media.external?.uri) {
            const url = media.external.uri;
            if (url.match(/\.(mp4|mov|webm|mpeg)(\?|$)/i)) {
              latestVideo = {
                url,
                rkey: post.uri.split("/").pop(),
                thumb: media.external.thumb?.fullsize || media.external.thumb?.thumb || ""
              };
            }
          }
        }
      }

      if (latestPhoto && latestVideo) break;
    }

    // DISPLAY PHOTO
    if (latestPhoto) {
      document.getElementById("latest-photo").innerHTML = `
        <a href="https://bsky.app/profile/${handle}/post/${latestPhoto.rkey}" target="_blank" rel="noopener">
          <img src="${latestPhoto.url}" style="max-width:100%;border-radius:12px;">
        </a>
      `;
    } else {
      document.getElementById("latest-photo").textContent = "No recent photo.";
    }

    // DISPLAY VIDEO
    if (latestVideo) {
      document.getElementById("latest-video").innerHTML = `
        <a href="https://bsky.app/profile/${handle}/post/${latestVideo.rkey}" target="_blank" rel="noopener">
          <video controls style="max-width:100%;border-radius:12px;" ${latestVideo.thumb ? `poster="${latestVideo.thumb}"` : ""}>
            <source src="${latestVideo.url}" type="video/mp4">
            Your browser does not support the video tag.
          </video>
        </a>
      `;
    } else {
      document.getElementById("latest-video").textContent = "No recent video.";
    }

  } catch (err) {
    console.error("Error loading Bluesky media:", err);
    document.getElementById("latest-photo").textContent = "Failed to load photo.";
    document.getElementById("latest-video").textContent = "Failed to load video.";
  }
}

// Run it
loadBlueskyMedia();
</script>

  
  <!-- Hub text -->
  <!--  <p>The turtles racked up 12.6k+ followers and 300k+ likes on TikTok—but I've moved all their content off the platform. You can now follow them on Bluesky <a href="https://bsky.app/profile/turtles.raych.com">@turtles.raych.com</a>.</p>
    <p>I have three red-eared slider turtles, all around 20 years old. I also keep two 20-gallon planted guppy tanks and share my home with a senior squad of dogs (down to one 😭😭😭).</p>
-->