<script>
  export let size = 200;
  export let thickness = 30;
  export let tobjs;
  export let rotate = 270;
  export let bg = "white";

  let viewBox = `0 0 ${size} ${size}`;
  let radius = size / 2;
  let halfCircumference = Math.PI * radius;

  $: {
    for (let tobj of tobjs) {
      let offset = halfCircumference * tobj.start;
      tobj.pieSize = halfCircumference * (tobj.end - tobj.start);
      tobj.dashArray = `0 ${offset} ${tobj.pieSize} ${
        halfCircumference - tobj.pieSize
      }`;
    }
  }
</script>

<svg width={size} height={size} {viewBox}>
  {#each tobjs as tobj}
    <circle
      r={radius / 2}
      stroke={tobj.color}
      stroke-width={radius}
      stroke-dasharray={tobj.dashArray}
      transform={`translate(${radius} ${radius}) rotate(${rotate})`}
    />
  {/each}
  <circle r={radius - thickness} cx={radius} cy={radius} fill={bg} />
</svg>
