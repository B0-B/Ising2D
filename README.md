# Ising2D
Live & interactive client side Monte Carlo simulation of a 2-dimensional Ising model. 


[Demo](https://codepen.io/b0-b/full/poRGmmv).


## Intro
This project aimed for a short interactive code to run an Ising simulation in 2 dimensions and within a browser. The Ising model is a naive spin lattice model governed by a simple energy equation to describe the magnetization of an arbitrary material. Monte Carlo methods further allow to peer into the dynamics of atomic magnetization. Here is a code that uses an optimized Metropolis-Hastings algorithm which includes a rejection method to sample new states from the thermodynamic probability distribution. Although this distribution consists of rules obtained from statistical ensemble physics this algorithm focuses on information only, treating the phys. quantities purely statistical. Hence, a new state is derived (sampled) if its entropically favorable to mimick the 2nd law of thermodynamics. At critical temperature the system starts to show order while the magnetization is non-zero. A magnetization estimate drawn from the lattice at low temperatures agrees with observations which shows that materials can get magnetized as they undergo a structural phase transition.

## Usage

You can copy and paste the script into your browser. 
`Note`: If embedded, the script will build all elements
relative to the parent container which should have a fixed width and height.
```html
<script>
    /* Ising Model in 2D with Periodic Boundaries */
    // ================ parameters ===================
    var     T = 1, // temperature in Kelvin
            J = 1.0; // coupling constant
    const   K = 1.0, // Boltzmann Constant
            L = 200, // matrix size (LxL)
            I = 'hot',// init hot/cold
            C = ['#d64b7e', '#23594f'], // colors up/down
            N = 10e+7,
            S = [1,-1], // spin states
            R = 1000; // rendering after how many steps
    /*================================================*/
    var d=document,p,t,w,m,dH,v,cs,pm={'T':T,'J':J};function u(){return Math.random()}
    function A(){s=0;for(let i=0;i<L;i++){for(let j=0;j<L;j++){s+=1*m[i][j].value}}return s/L/L}
    async function MH(){const i=Math.floor(u()*(L-1)),j=Math.floor(u()*(L-1)),c=m[i][j],
    dH=2*pm.J*c.value*(1*m[(i+1)%L][j].value+1*m[(L+i-1)%L][j].value+1*m[i][(j+1)%L].value
    +1*m[i][(L+j-1)%L].value);if(dH<0||u()<Math.exp(-dH/K/pm.T)){c.value*=-1}}
    async function render(){for(let i=0;i<L;i++){for(let j=0;j<L;j++){m[i][j].style
    .backgroundColor=C[S.indexOf(1*m[i][j].value)]}}await new Promise((r)=>{setTimeout(()=>
    {r(0)},5)})}async function sim(){for(let i=0;i<N;i++){await MH();if(i%R==0)
    {await render();d.getElementById('mag').innerHTML='Magnetization: '+A()}}} 
    p=d.currentScript.parentNode,w=d.createElement('div'),m=[];t=d.createElement('table'); 
    t.style.width=p.style.width;t.style.height=p.style.width;t.style.fontSize="0";
    cs=`${p.clientWidth/L}px`;t.style.display="inline-block";p.appendChild(w); 
    w.appendChild(t);t.cellSpacing='0';for(let i=0;i<L;++i){const row=d.createElement('tr'),
    r=[];row.style.borderSpacing='0';row.style.borderCollapse='collapse';t.appendChild(row);
    for(let j=0;j<L;j++){const C=d.createElement('td');C.name=`${j}`;C.style.height=cs;
    C.style.width=cs;C.style.padding="0";v=1;if(I=='hot'){v=[0,1][Math.round(u())]}
    C.value=S[v].toString();row.appendChild(C);r.push(C)}m.push(r)} 
    const fd=d.createElement('div'),M=d.createElement('div');M.id='mag';fd.appendChild(M);
    ['T','J'].forEach(x=>{const xn=x,l=d.createElement('label'),In=d.createElement('input'),
    out=d.createElement('output');l.innerHTML=xn;w.appendChild(fd); 
    if(x=='T'){In.min="1e-2";In.step="1e-2";In.max="50"}else{In.min="1e-4";In.step="1e-4";In.max="10"}
    In.type='range';In.id=xn;In.style.minWidth=`${t.clientWidth}px`;In.value=`${pm[x]}`;
    In.oninput=function(){pm[xn]=this.value;out.innerHTML=`${this.value}`}
    l.htmlFor=In.id;out.innerHTML=`${pm[x]}`;[l,In,out,d.createElement('br')]
    .forEach(e=>{fd.appendChild(e)})});sim()//________https://github.com/B0-B/
</script>
```

## Performance
Browsers may experience some performance issues depending on the parameters set.
Default parameters were successfully tested in chromium & firefox.