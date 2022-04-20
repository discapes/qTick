<script>
  export let size = 200;
  export let thickness = 30;
  export let tobjs;
  export let bg = "white";

  let viewBox = `0 0 ${size} ${size}`;
  let radius = size / 2;
  let halfCircumference = Math.PI * radius;

  $: {
    for (let tobj of tobjs) {
      tobj.pieSize = halfCircumference * (tobj.end - tobj.start+ (tobj.end < tobj.start));
      tobj.dashArray = `${tobj.pieSize} ${
        halfCircumference - tobj.pieSize
      }`;
    }
  }
</script>

<svg width={size} height={size} {viewBox}>
  {#each tobjs as tobj}
    <circle
      r={radius / 2}
      cx={radius}
      cy={radius}
      stroke-linejoin="arcs"
      stroke={tobj.color}
      stroke-width={radius}
      fill-opacity="0"
      stroke-opacity="0.5"
      stroke-dasharray={tobj.dashArray}
      stroke-dashoffset={-halfCircumference * tobj.start + 1/4*halfCircumference}
    />
  {/each}
  <circle r={radius - thickness} cx={radius} cy={radius} fill={bg} />
</svg>
