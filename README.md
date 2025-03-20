# Shader-based Live Performance
My first attempt at a live, shader-coding- based performance in Shawn Lawson's the Force. This was really though for me! I wanted to record a perfomance to my favorite U2 track of all-time, "Until the End of the World." But aesthetically, I wanted to mimic what U2 did for their video for their track "Lemon." So lots of black and whites, grid shapes, and simple aesthetics. 

The reason I chose Until the End of the World to perform to, is because it has a lot of musical crescendos - which I was hoping to tie into my shader functions, and have them gradually become more and more intense. This proved to be very difficult! I reached a stage where instead of live-coding the whole thing, I effectively ended up making a template where I could interactively play around with the parameters of my shader's functions, but even then I would reach a certain point where the music got so fast and the screen got so filled, that it became hard to actually see the code and where my cursor was. All things said and done, I am relatively proud of my first attempt! I definitely want to continue playing around with this! Shaders are extremely cool.

## Video link: 
https://drive.google.com/file/d/1RaV4NCnkCUrmFKZT-obGe3KYq4AvcbyU/view?usp=sharing

## Shader file
Shader file is in the main repo! The Force saves it out as a .frag file, so hopefully that is correct :)

## Code:
```
void main() {
  vec2 p = uv();
  float color = .0; 
  float gain = 1.2;
  float thickness = .00236; 
  float rotationSpeed = bands[2] * 0.00002;
  float angle = time * rotationSpeed;  
  mat2 rotationMatrix = mat2(cos(angle), -sin(angle), sin(angle), cos(angle));
  p = rotationMatrix * p;  

  for( float i = 2.1; i < 5.; i++ ) { 
    p.x += sin(p.y + time / i) * gain * bands[1] * .2;

  
    p.x += 0.0002 * sin(p.y * 25.0 + time * 8.0) * bands[1]; 
    p.x += 0.002 * sin(p.y * 50.0 + time * 15.0) * bands[2]; 
    p.x += 0.002 * sin(p.y * 15.0 + time * 11.0) * bands[3]; 
    p.y += 0.0 * sin(p.x * 1.0 + time * 18.0) * bands[1]; 
    p.y += 0.0 * sin(p.x * 10.0 + time * 18.0) * bands[2]; 
    p.y += 0.0 * sin(p.x * 10.0 + time * 18.0) * bands[3]; 
    
    const int circleCount = 0; 
  for (int j = 0; j < circleCount; j++) { 
    float offset = float(j) * 0.8; 
    float circleTime = mod(time + offset, 1.0); 
    float radius = circleTime * 0.1 * bands[2]; 

    vec2 center = vec2(0.0, float(j) * 0.4 - 0.5); 
    float dist = length(p - center); 

    float circleThickness = 0.002; 
    float circleEdge = smoothstep(radius - circleThickness, radius, dist) - smoothstep(radius, radius + circleThickness, dist);
    
    color += circleEdge * bands[3]; 
  }
    color += abs(thickness / p.x);
  }

  vec3 bgColor = vec3(0., 0., 0.); 
  vec3 lineColor = vec3(0.8, .8, .8); 
  
  float pulse = sin(time * 1.0) * 0.5 + 0.5; 

  if (bands[2] > 0.8) {
    float whiteIntensity = sin(time * 5.0) * 0.5; 
    lineColor = mix(lineColor, vec3(1.0, 1.0, 1.0), whiteIntensity); 
    thickness += pulse * 0.1; 
  }

  float gridFrequency = 1.0;
  float gridThickness = 0.001;
  float gridOpacity = 1.0 + 1.0 * sin(time * 0.1); 
  float gridRotationSpeed = 0.0;  
  float gridAngle = time * gridRotationSpeed;  
  mat2 gridRotationMatrix = mat2(cos(gridAngle), -sin(gridAngle), sin(gridAngle), cos(gridAngle));
  vec2 gridPos = gridRotationMatrix * p;  
  float gridX = mod(gridPos.x, 0.4 / gridFrequency);
  float gridY = mod(gridPos.y, 0.4 / gridFrequency);
  
  float gridLineX = step(gridX, gridThickness);
  float gridLineY = step(gridY, gridThickness);
  
  vec3 gridColor = vec3(0.5, 0.5, 0.5) * gridOpacity; 
  vec3 gridEffect = mix(bgColor, gridColor, gridLineX + gridLineY); 
  
  vec3 finalColor = mix(gridEffect, lineColor, color * bands[3]);

  gl_FragColor = vec4(finalColor, 1.0);  
}
```

