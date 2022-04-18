<script>
    export let size = 200;
      export let thickness = 30;
      export let stuff;
      export let rotate = 270;
      export let bg = "white";
    
    let viewBox = `0 0 ${size} ${size}`;
    let radius = size / 2;
    let halfCircumference = Math.PI * radius;
      
      for (let thing of stuff) {
        let offset = halfCircumference * (thing.start);
        thing.pieSize = halfCircumference * (thing.end - thing.start);
        thing.dashArray = `0 ${offset} ${thing.pieSize} ${halfCircumference - thing.pieSize}`;
      }
  </script>
  
  <svg width={size} height={size} {viewBox}>
  
      {#each stuff as thing}
            <circle
      r={radius /2}
      stroke={thing.color}
      stroke-width={radius}
      stroke-dasharray={thing.dashArray}
          transform={`translate(${radius} ${radius}) rotate(${rotate})`}
    />
      
      {/each}
        <circle r={radius-thickness} cx={radius} cy={radius} fill={bg}/>
  </svg>