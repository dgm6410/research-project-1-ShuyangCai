# research-project-1-ShuyangCai
research-project-1-ShuyangCai created by GitHub Classroom

In this assignment, I researched how to simulate the wave of the water. In the beginning, I wanna use C# to control the changes of every pixel. However, it is not convenient and efficient. So, I learned how to use a new weapon “Shader Graph” to design water in Unity. In this process, I read the document online. Even though it is really interesting and simple, it can’t satisfy my requirement, because I will make many calculate to get the real-time change of the UV. So, finally, I found some books to learn CG language and how GPU works.

In order to understand my programs, you should learn some basic knowledge about the process in rending[1]. This part is also important for my following assignments. The procedure in rending has a strict rule. There are 4 kinds of default shaders in Unity. According to my requirements in this week, I chosen Unlit shader to simulate water (Maybe I will change it in the next assignment because water is transparent).

[1]https://unity3d.com/cn/learn/tutorials/topics/graphics/gentle-introduction-shaders

So, let’s talk about how to simulate the wave of the water. I designed 2 different ways of waves’ moving. 

1.The first one is flowing water, which will be used in the river. Thus, the first thing I did is to let the UV move according to the time.  

float2 flowSpeed = float2(_SpeedX * _Time.y, _SpeedY * _Time.y);

In order to make it more realistic, I also set the time-varying offsets in UV according to noise picture (gray scale picture). In my program, the direction of the offset is (1，1).

float noise = tex2D(_NoiseTex, uv).r * _Intensity;
float2 speed = normalize(float2(1.0,1.0));
float4 col = tex2D(_MainTex, uv + flowSpeed + noise * sin(_Time.y * speed)*_WaveScale);  

What is worth mentioning is that I used sine function to simulate the noise changes, because it is period, continuous, and works in [-1,1].

The second one is about clam water, which will be used in the pool. This shader is made by 2 parts, overall fluctuation and waves from objects.

float noise = tex2D(_NoiseTex, uv).r * _Intensity;
float2 uvDir = normalize(uv - fixed2(0.5, 0.7));			
float dis = distance(uv, fixed2(0.5, 0.7));
float4 col = tex2D(_MainTex, uv + noise * sin(_Time.y * speed) + _WaveStrength * uvDir*sin(_Time.y*_TimeScale + dis * _WaveFactor)/5/dis);

Overall fluctuation is similar with the noise in the flowing river we talked about just now. For the waves from objects. I set the position of the object is (0.5,0.5), and then calculated the related data of different pixel, including direction and distance. Then I used sine function to simulate the ripples. The ripples will decrease according to the distance’s increasing. 
