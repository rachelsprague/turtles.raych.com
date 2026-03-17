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
    <a href="https://www.twitch.tv/raych_com" target="_blank" rel="noopener" aria-label="Twitch">
      <i class="fab fa-twitch"></i>
    </a>
  </div>

  <!-- Latest Media Section -->
  <section class="latest">
    <h2>🐠 Aquarium Livestream 🐠</h2>
    <div id="twitch-status" class="twitch-card">
      <a href="https://www.twitch.tv/raych_com" target="_blank" rel="noopener">
        <div class="text-block">
          <strong id="twitch-text">🐠 Stream Offline</strong>
          <span class="duration"></span>
        </div>
      </a>
    </div>

    <h2>Latest Photo</h2>
    <div id="latest-photo"></div>

    <h2>Latest Video</h2>
    <div id="latest-video"></div>
  </section>

  <!-- Links Section -->
  <h2>Links</h2>
  <a href="/animals" rel="noopener">Animals</a>
  <a href="/tanks" rel="noopener">Aquariums</a>
  <a href="https://www.twitch.tv/raych_com" target="_blank" rel="noopener">🐠 Aquarium Livestream 🐠</a>
  <a href="https://open.spotify.com/user/31ekhjd5x5qoyln7zo4zkv4tneay" target="_blank" rel="noopener">🎧 Playlists 🎧</a>
  <a class="btn btn--primary btn--large btn--block" href="https://www.raych.com" target="_blank" rel="noopener">Raych</a>
  <a class="btn btn--primary btn--large btn--block" href="http://www.raych.com/makes/" target="_blank" rel="noopener">Raych Makes</a>

</div>

<script>
/* ================================
   Load latest Bluesky media
   ================================ */
async function loadBlueskyMedia() {
  const handle = "turtles.raych.com";
  const photoEl = document.getElementById("latest-photo");
  const videoEl = document.getElementById("latest-video");

  try {
    const res = await fetch(
      `https://public.api.bsky.app/xrpc/app.bsky.feed.getAuthorFeed?actor=${encodeURIComponent(handle)}&filter=posts_with_media&limit=50`
    );
    const data = await res.json();

    let latestPhoto = null;
    let latestVideo = null;

    for (const item of (data.feed || [])) {
      const post = item.post;
      if (!post) continue;
      if (post.reason?.$type === "app.bsky.feed.defs#reasonRepost") continue;

      const embed = post.embed;
      if (!embed) continue;

      // Photo
      if (!latestPhoto && embed.$type === "app.bsky.embed.images#view" && embed.images?.length > 0) {
        const img = embed.images[0];
        latestPhoto = { url: img.fullsize || img.thumb, rkey: post.uri.split("/").pop() };
      }

      // Video
      if (!latestVideo) {
        if ((embed.$type === "app.bsky.embed.video#view" || embed.$type === "app.bsky.embed.video#viewExternal") && embed.playlist) {
          latestVideo = { url: embed.playlist, rkey: post.uri.split("/").pop(), thumb: embed.thumbnail || "" };
        } else if (embed.$type === "app.bsky.embed.external#view" && /\.(mp4|mov|webm|mpeg)(\?|$)/i.test(embed.external?.uri || "")) {
          latestVideo = {
            url: embed.external.uri,
            rkey: post.uri.split("/").pop(),
            thumb: embed.external.thumb?.fullsize || embed.external.thumb?.thumb || ""
          };
        } else if (embed.$type === "app.bsky.embed.recordWithMedia#view" && embed.media) {
          const media = embed.media;
          if ((media.$type === "app.bsky.embed.video#view" || media.$type === "app.bsky.embed.video#viewExternal") && media.playlist) {
            latestVideo = { url: media.playlist, rkey: post.uri.split("/").pop(), thumb: media.thumbnail || "" };
          }
        }
      }

      if (latestPhoto && latestVideo) break;
    }

    // Display photo
    if (latestPhoto && photoEl) {
      photoEl.innerHTML = `
        <a href="https://bsky.app/profile/${handle}/post/${latestPhoto.rkey}" target="_blank" rel="noopener">
          <img src="${latestPhoto.url}" style="max-width:100%;border-radius:12px;">
        </a>`;
    } else if (photoEl) photoEl.textContent = "No recent photo.";

    // Display video
    if (latestVideo && videoEl) {
      videoEl.innerHTML = `
        <a href="https://bsky.app/profile/${handle}/post/${latestVideo.rkey}" target="_blank" rel="noopener">
          <video controls style="max-width:100%;border-radius:12px;" ${latestVideo.thumb ? `poster="${latestVideo.thumb}"` : ""}>
            <source src="${latestVideo.url}">
            Your browser does not support the video tag.
          </video>
        </a>`;
    } else if (videoEl) videoEl.textContent = "No recent video.";

  } catch (err) {
    console.error("Failed to load Bluesky media:", err);
    if (photoEl) photoEl.textContent = "Failed to load photo.";
    if (videoEl) videoEl.textContent = "Failed to load video.";
  }
}

/* ================================
   Update Twitch stream status
   ================================ */
async function updateTwitchStatus() {
  try {
    const res = await fetch("https://decapi.me/twitch/uptime/raych_com");
    const text = await res.text();

    const card = document.getElementById("twitch-status");
    const textBlock = card.querySelector(".text-block");
    const mainText = textBlock.querySelector("#twitch-text");
    const durationEl = textBlock.querySelector(".duration");

    // Animate fade-in/out
    card.style.opacity = 0;
    card.style.transform = "translateY(4px)";

    setTimeout(() => {
      if (text.toLowerCase().includes("offline")) {
        mainText.textContent = "🐠 🐢 Aquarium stream offline";
        durationEl.textContent = "";
        card.classList.remove("live");
      } else {
        mainText.textContent = "🐠 🐢 Live on Twitch";
        durationEl.textContent = text;
        card.classList.add("live");
      }

      card.style.opacity = 1;
      card.style.transform = "translateY(0)";
    }, 300);

  } catch (err) {
    console.error("Failed to fetch Twitch status:", err);
    const card = document.getElementById("twitch-status");
    const textBlock = card.querySelector(".text-block");
    textBlock.querySelector("#twitch-text").textContent = "🐠 Stream status unavailable";
    textBlock.querySelector(".duration").textContent = "";
  }
}

// Initial load + refresh every 60s
loadBlueskyMedia();
updateTwitchStatus();
setInterval(updateTwitchStatus, 60000);
</script>
  
  <!-- Hub text -->
  <!--  <p>The turtles racked up 12.6k+ followers and 300k+ likes on TikTok—but I've moved all their content off the platform. You can now follow them on Bluesky <a href="https://bsky.app/profile/turtles.raych.com">@turtles.raych.com</a>.</p>
    <p>I have three red-eared slider turtles, all around 20 years old. I also keep two 20-gallon planted guppy tanks and share my home with a senior squad of dogs (down to one 😭😭😭).</p>
-->