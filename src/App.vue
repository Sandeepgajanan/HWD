<template>
  <div class="experience" @click="beginExperience">
    <!-- INTRO SCREEN -->
    <div v-if="!started" class="intro">
      <h1 class="intro-line">{{ displayedText }}</h1>
    </div>

    <!-- STORY -->
    <div v-else class="story">
      <div class="ballons"></div>

      <p class="line">{{ displayedText }}</p>

      <div class="story_part">
        <img :src="photoSrc" ref="photo" class="photo" />

        <h2 ref="final" class="final">
          Happy Women’s Day, {{ name }} <br />
          You are unstoppable.
        </h2>
      </div>
    </div>

    <audio ref="bgMusic" src="/womensday.mp3"></audio>
  </div>
</template>

<script>
import gsap from "gsap";
export default {
  data() {
    return {
      name: "",
      started: false,
      displayedText: "",
      introLines: [],
      lines: [
        "Today is not just another day...",
        "Today, we celebrate strength.",
        "We celebrate courage.",
        "We celebrate Kindness.",
        "So today… we celebrate YOU.",
      ],
      photoSrc: "",
    };
  },

  mounted() {
    const params = new URLSearchParams(window.location.search);
    const key = params.get("name");

    if (key) {
      const lowerKey = key.toLowerCase();
      this.name = lowerKey.charAt(0).toUpperCase() + lowerKey.slice(1);

      // since images are in public folder
      this.photoSrc = `/${lowerKey}.png`;
    } else {
      this.name = "Team Member";
      this.photoSrc = "/default.png";
    }
    // Intro lines dynamic
    this.introLines = [
      `Hi ${this.name}...`,
      "You know… background music makes everything perfect.",
      "So tap anywhere… let the story begin!!",
    ];

    this.typeIntro(0);
  },

  methods: {
    typeIntro(index) {
      if (index >= this.introLines.length) return;

      const text = this.introLines[index];
      this.displayedText = "";
      let i = 0;

      const typing = setInterval(() => {
        this.displayedText += text.charAt(i);
        i++;

        if (i >= text.length) {
          clearInterval(typing);

          setTimeout(() => {
            this.typeIntro(index + 1);
          }, 800);
        }
      }, 50);
    },
    beginExperience() {
      if (this.started) return;

      this.started = true;

      const audio = this.$refs.bgMusic;
      audio.volume = 0.6;
      audio.play().catch(() => {});

      this.$nextTick(() => {
        this.createballons();
        this.typeLines(0);
      });
    },

    typeLines(index) {
      if (index >= this.lines.length) {
        this.showFinalScene();
        return;
      }

      const text = this.lines[index];
      this.displayedText = "";
      let i = 0;

      const typing = setInterval(() => {
        this.displayedText += text.charAt(i);
        i++;

        if (i >= text.length) {
          clearInterval(typing);

          setTimeout(() => {
            this.eraseLine(() => {
              this.typeLines(index + 1);
            });
          }, 1500);
        }
      }, 60);
    },

    eraseLine(callback) {
      let text = this.displayedText;

      const erasing = setInterval(() => {
        text = text.slice(0, -1);
        this.displayedText = text;

        if (text.length === 0) {
          clearInterval(erasing);
          callback();
        }
      }, 30);
    },

    showFinalScene() {
      const tl = gsap.timeline();

      tl.to(this.$refs.photo, {
        opacity: 1,
        scale: 1.1,
        duration: 2,
      }).to(this.$refs.final, {
        opacity: 1,
        duration: 2,
        delay: 0.5,
        onComplete: this.fadeOutMusic,
      });
    },

    createballons() {
      const container = document.querySelector(".ballons");

      for (let i = 0; i < 25; i++) {
        const ballon = document.createElement("img");
        ballon.src = "/ballon1.svg";
        ballon.classList.add("ballon");

        ballon.style.left = Math.random() * 100 + "vw";
        ballon.style.animationDuration = 6 + Math.random() * 6 + "s";

        container.appendChild(ballon);
      }
    },

    fadeOutMusic() {
      const audio = this.$refs.bgMusic;

      const fade = setInterval(() => {
        if (audio.volume > 0.05) {
          audio.volume -= 0.05;
        } else {
          clearInterval(fade);
          audio.pause();
        }
      }, 200);
    },
  },
};
</script>

<style>
@font-face {
  font-family: "D";
  src: url("/DancingScript-VariableFont_wght.ttf");
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html,
body {
  width: 100%;
  height: 100%;
}

.experience {
  height: 100vh;
  width: 100%;
  background: radial-gradient(circle at center, #2d004d, #000);
  color: white;
  font-family: "D", cursive;

  text-align: center;
  overflow: hidden;
  cursor: pointer;
  position: relative;
  padding: 2rem;
}
.intro {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
/* ================= INTRO ================= */

.intro-line {
  font-size: 2rem;
}

/* ================= STORY ================= */

.story {
  position: relative;
  width: 100%;
  height: 100%;
  justify-content: center;
  align-items: center;
}

/* TYPEWRITER TEXT CENTERED */
.line {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 2rem;
}

/* ================= PHOTO + FINAL ================= */

.story_part {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 3rem;
  width: 100%;
  height: 100%;
}

.photo {
  opacity: 0;
  width: 260px;
  border-radius: 8px;
  /* transform: scale(0.8); */
  transition: all 0.3s ease;
}

.final {
  opacity: 0;
  font-size: 2.8rem;
  text-shadow: 0 0 20px #ff69b4;
  line-height: 1.3;
}



.ballons {
  position: absolute;
  width: 100%;
  height: 100%;
  pointer-events: none;
}

.ballon {
  position: absolute;
  width: 20px;
  top: -50px;
  animation: fall linear infinite;
}

@keyframes fall {
  to {
    transform: translateY(110vh) rotate(360deg);
  }
}

/* ================= RESPONSIVE ================= */

/* Tablets */
@media (max-width: 1024px) {
  .line {
    font-size: 2.5rem;
    /* top: 30%; */
  }

  .final {
    font-size: 2.2rem;
  }

  .photo {
    width: 220px;
  }
}

/* Mobile */
@media (max-width: 768px) {
  .line {
    font-size: 2rem;
   
  }

  .story_part {
    flex-direction: column;
    gap: 1.5rem;
    bottom: 8%;
  }

  .photo {
    width: 180px;
  }

  .final {
    font-size: 1.8rem;
  }
}

/* Small Mobile */
@media (max-width: 480px) {
  .line {
    font-size: 1.6rem;
  }

  .final {
    font-size: 1.5rem;
  }

  .photo {
    width: 150px;
  }
}
</style>
