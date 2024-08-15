<script setup lang="ts">
import { onMounted, ref } from 'vue';
import data from './assets/data/data';
import gsap from 'gsap';

    const activeColors = ['#5fa5f9', '#e879f9', '#a78vfa', '#2cd4bf'];
    const container = ref(null);

    const shuffleArray = (array: any[]) => {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
    };

    const generatePads = (card: any, activePadCount:any, items:any) => {
      const rowsConfig = [7, 7, 7, 7, Math.floor(Math.random() * 3) + 2];
      const clickablePads: any[] = [];

      rowsConfig.forEach((padCount, rowIndex) => {
        const row = document.createElement('div');
        row.classList.add('row');

        for (let i = 0; i < padCount; i++) {
          const pad = document.createElement('div');
          pad.classList.add('pad');
          row.appendChild(pad);

          if (rowIndex !== 0 && rowIndex !== rowsConfig.length - 1) {
            clickablePads.push(pad);
          }
        }

        card.appendChild(row);
      });

      shuffleArray(clickablePads);
      setActivePads(clickablePads, card, activePadCount, items);
    };

    const setActivePads = (clickablePads: any, card: any, activePadCount: any, items: any) => {
      clickablePads.slice(0, activePadCount).forEach((pad: any, i: any) => {
        pad.classList.add('active');
        pad.style.backgroundColor = activeColors[Math.floor(Math.random() * activeColors.length)];

        pad.addEventListener('click', () => {
          clickablePads.forEach((p: any) => (p.style.zIndex = '0'));
          pad.style.zIndex = '1';

          const item = items[i];
          const cardContent = card.querySelector('.card-content');

          cardContent.innerHTML = `
            <button class='close-btn'>Back</button>
            <div class='card-item img'>
              <img src='${item.img}' alt='' />
            </div>
            <div class='card-item copy'>
              <h1>${item.h1}</h1>
              <p>${item.copy}</p>
            </div>
            <div class='card-item copy link'>
              <a href='${item.linkSrc}'>${item.linkLabel}</a>
              <ion-icon name='chevron-forward-outline'></ion-icon>
            </div>
          `;

          gsap.to(pad, {
            scale: 20,
            duration: 0.3,
            onComplete: () => {
              gsap.to(cardContent, {
                opacity: 1,
                pointerEvents: 'all',
                duration: 0.075,
              });
              gsap.fromTo (cardContent.querySelectorAll('.card-item'), {
                y: 100,
                rotation: () => gsap.utils.random(-30, 30),
                opacity: 0,
              }, {
                y: 0,
                rotation: 0,
                opacity: 1,
                duration: 2,
                ease: 'elastic.out',
                stagger: 0.1
              }

            );
            },
          });

          card.querySelector('button').addEventListener('click', () => {
            gsap.to(cardContent, {
              opacity: 0,
              pointerEvents: 'none',
              duration: 0.2,
              onComplete: () => {
                gsap.to(pad, {
                  scale: 1,
                  duration: 0.3,
                  onComplete: () => {
                    pad.style.zIndex = '0';
                    cardContent.style.opacity = 0;
                    cardContent.style.pointerEvents = 'none';
                  },
                });
              },
            });
          });
        });
      });
    };

    onMounted(() => {
      const containerElement: any = container.value;

      data.forEach((monthData) => {
        const month = Object.keys(monthData)[0];
        const items = monthData[month];

        const card = document.createElement('div');
        card.classList.add('card');

        const cardTitle = document.createElement('div');
        cardTitle.classList.add('card-title');
        cardTitle.innerHTML = `<p>${month}</p>`;
        card.appendChild(cardTitle);

        const cardContent = document.createElement('div');
        cardContent.classList.add('card-content');
        card.appendChild(cardContent);

        generatePads(card, items.length, items);
        containerElement.appendChild(card);
      });
    });


</script>

<template>
    <div class="container" ref="container">
      <nav>
        <div class="logo">
          <img src="./assets/images/logo.jpg" alt="">
        </div>
        <div class="nav-items">
          <a href="#">blog</a>
          <a href="#">contact</a>
        </div>
      </nav>
    </div>
</template>

<style scoped>
</style>
