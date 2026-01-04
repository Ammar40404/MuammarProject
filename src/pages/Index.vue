<template>
  <div class="home-page">
    <Header 
      :active-section="currentSection" 
      @change-section="changeSection"
    />
    
    <Navigation 
      :current-section="currentSection"
      @nav-click="changeSection"
    />
    
    <div class="sections-container" ref="sectionsContainer">
      <Section1 
        :active="currentSection === 0"
        ref="section1"
      />
      <Section2 
        :active="currentSection === 1"
        @next-section="nextSection"
      />
      <Section3 
        @restart="restartJourney"
      />
    </div>
  </div>
</template>

<script>
import Header from '@/components/Header.vue'
import Navigation from '@/components/Navigation.vue'
import Section1 from '@/components/Section1.vue'
import Section2 from '@/components/Section2.vue'
import Section3 from '@/components/Section3.vue'

export default {
  name: 'Index',
  components: {
    Header,
    Navigation,
    Section1,
    Section2,
    Section3
  },
  data() {
    return {
      currentSection: 0,
      isScrolling: false,
      scrollTimeout: null
    }
  },
  mounted() {
    this.setupKeyboardNavigation()
    this.setupWheelNavigation()
    this.initScrollSnap()
  },
  methods: {
    changeSection(index) {
      if (index === this.currentSection || this.isScrolling) return
      
      this.isScrolling = true
      this.currentSection = index
      
      const container = this.$refs.sectionsContainer
      const sectionHeight = window.innerHeight
      container.scrollTo({
        top: sectionHeight * index,
        behavior: 'smooth'
      })
      
      clearTimeout(this.scrollTimeout)
      this.scrollTimeout = setTimeout(() => {
        this.isScrolling = false
      }, 800)
    },
    
    nextSection() {
      if (this.currentSection < 2) {
        this.changeSection(this.currentSection + 1)
      }
    },
    
    restartJourney() {
      this.changeSection(0)
    },
    
    setupKeyboardNavigation() {
      window.addEventListener('keydown', (e) => {
        if (this.isScrolling) return
        
        if (e.key === 'ArrowDown' || e.key === 'ArrowRight' || e.key === 'PageDown') {
          e.preventDefault()
          if (this.currentSection < 2) {
            this.changeSection(this.currentSection + 1)
          }
        } else if (e.key === 'ArrowUp' || e.key === 'ArrowLeft' || e.key === 'PageUp') {
          e.preventDefault()
          if (this.currentSection > 0) {
            this.changeSection(this.currentSection - 1)
          }
        } else if (e.key === 'Home') {
          e.preventDefault()
          this.changeSection(0)
        } else if (e.key === 'End') {
          e.preventDefault()
          this.changeSection(2)
        }
      })
    },
    
    setupWheelNavigation() {
      let wheelTimeout
      let lastScrollTime = 0
      
      window.addEventListener('wheel', (e) => {
        if (this.isScrolling) return
        
        const now = Date.now()
        if (now - lastScrollTime < 500) return
        
        clearTimeout(wheelTimeout)
        wheelTimeout = setTimeout(() => {
          lastScrollTime = now
          
          if (e.deltaY > 50 && this.currentSection < 2) {
            this.changeSection(this.currentSection + 1)
          } else if (e.deltaY < -50 && this.currentSection > 0) {
            this.changeSection(this.currentSection - 1)
          }
        }, 150)
      }, { passive: true })
    },
    
    initScrollSnap() {
      const container = this.$refs.sectionsContainer
      
      container.addEventListener('scroll', () => {
        if (this.isScrolling) return
        
        const scrollTop = container.scrollTop
        const sectionHeight = window.innerHeight
        const newSection = Math.round(scrollTop / sectionHeight)
        
        if (newSection !== this.currentSection) {
          this.currentSection = newSection
        }
      })
    }
  }
}
</script>

<style scoped>
.home-page {
  height: 100vh;
  width: 100vw;
  overflow: hidden;
  position: relative;
}

.sections-container {
  height: 100vh;
  width: 100vw;
  overflow-y: auto;
  scroll-snap-type: y mandatory;
  scroll-behavior: smooth;
}

.sections-container::-webkit-scrollbar {
  display: none;
}

.sections-container {
  -ms-overflow-style: none;
  scrollbar-width: none;
}

.sections-container > section {
  scroll-snap-align: start;
  scroll-snap-stop: always;
  height: 100vh;
  width: 100vw;
}
</style>