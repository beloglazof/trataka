<script>
  const praiseImg1 = 'images/praise/1.png';
  const praiseImg2 = 'images/praise/2.png';
  const praiseImg3 = 'images/praise/3.png';
  const praiseImg4 = 'images/praise/4.png';
  const praiseImg5 = 'images/praise/5.png';
  const praiseImg6 = 'images/praise/6.png';
  const praiseImg7 = 'images/praise/7.png';
  const praiseImg8 = 'images/praise/8.png';
  const praiseImg9 = 'images/praise/9.png';
  const praiseImg10 = 'images/praise/10.png';
  const praiseImg11 = 'images/praise/11.png';
  const praiseImg12 = 'images/praise/12.png';

  function getRandomInt(min, max) {
    const ceiledMin = Math.ceil(min);
    const flooredMax = Math.floor(max);

    // The maximum is exclusive and the minimum is inclusive
    return Math.floor(Math.random() * (flooredMax - ceiledMin)) + ceiledMin;
  }
  const praiseImageList = [
    praiseImg1,
    praiseImg2,
    praiseImg3,
    praiseImg4,
    praiseImg5,
    praiseImg6,
    praiseImg7,
    praiseImg8,
    praiseImg9,
    praiseImg10,
    praiseImg11,
    praiseImg12
  ];
  const praiseImg = praiseImageList[getRandomInt(0, praiseImageList.length)];
</script>

<div class="praise-box">
  <span class="praise-text">Ура! Все круги прочитаны!</span>
  <img class="praise-image" src={praiseImg} alt="Вы молодец!" width="300" height="300" />
</div>

<style>
  .praise-box {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .praise-image {
    margin-top: 1em;
  }
</style>
