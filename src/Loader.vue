<template>
  <div ref="loader" class="loader-wrapper">
    <div class="loader-content">
      <!-- circle -->
      <div class="circle-wrapper">
        <svg viewBox="0 0 140 140">
          <circle
            ref="circle"
            cx="70"
            cy="70"
            r="65"
            stroke="#846C64"
            stroke-width="4"
            fill="none"
            stroke-dasharray="0,408"
          />
        </svg>

        <span ref="circleText" class="circle-text">
          <p ref="headphoneText" class="headphone-text">
            Wear your headphones <br />
            for the best experience
          </p>
        </span>
      </div>

      <!-- headphone text -->

      <!-- start button -->
      <div ref="startButton" class="start-button" @click="startExperience">
        Click to start →
      </div>
    </div>
  </div>
</template>

<script>
import gsap from "gsap";

export default {
  emits: ["start"],
  data() {
    return {
      showStart: false,
    };
  },

  mounted() {
    const body = document.body;

    body.style.overflow = "hidden";
    body.style.height = "100vh";
    body.style.position = "fixed";
    body.style.width = "100%";

    const circle = this.$refs.circle;
    const text = this.$refs.headphoneText;
    const tl = gsap.timeline();

    tl.to(circle, {
      strokeDasharray: "408,408",
      duration: 2.5,
      ease: "power2.inOut",
    });

    tl.fromTo(
      text,
      { opacity: 0, y: 20 },
      { opacity: 1, y: 0, duration: 1 },
      "-=1.5",
    );

    tl.call(() => {
      this.showStart = true;

      gsap.to(this.$refs.startButton, {
        opacity: 1,
        y: 0,
        duration: 0.8,
        ease: "power3.out",
      });
    });
  },

  methods: {
    startExperience() {
      const loader = this.$refs.loader;
      const body = document.body;

      gsap.to(loader, {
        x: "100%",
        duration: 1.4,
        ease: "power4.inOut",
        onComplete: () => {
          body.style.overflow = "";
          body.style.height = "";
          body.style.position = "";
          body.style.width = "";

          this.$emit("start"); // trigger main page
        },
      });
    },
  },
};
</script>

<style>
.loader-wrapper {
  position: fixed;
  inset: 0;
  background: black;
  z-index: 9999;
  display: flex;
  align-items: center;
  justify-content: center;
}

.loader-content {
  text-align: center;
}

.circle-wrapper {
  position: relative;
  width: 200px;
  height: 200px;
  margin: auto;
}

.circle-wrapper svg {
  width: 100%;
  height: 100%;
}

.circle-text {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 40px;
}

.headphone-text {
  margin-top: 30px;
  color: white;
  font-size: 20px;
  line-height: 1.5;
}

.start-button {
  margin-top: 40px;
  padding: 14px 30px;
  border-radius: 40px;
  border: 1px solid rgba(255, 255, 255, 0.3);
  color: white;
  cursor: pointer;
  backdrop-filter: blur(10px);
  transition: 0.3s;
  opacity: 0;
  transform: translateY(20px);
}

.start-button:hover {
  background: rgba(255, 255, 255, 0.1);
}
</style>
