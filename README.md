# Ising2D
Live & interactive client side Monte Carlo simulation of a 2-dimensional Ising model. 


[Demo](https://codepen.io/b0-b/full/poRGmmv).


## Intro
This project aimed for a short interactive code running an Ising2D simulation in 2 dimensions - the highest dimension to have an exact solution, found by [Onsager](https://journals.aps.org/pr/abstract/10.1103/PhysRev.65.117) in 1944. The numerical method however uses an optimized Metropolis-Hastings algorithm which includes a rejection method to sample new states from the thermodynamic probability distribution. This allows to simulate the spontaneous magnetization in a (ferromagnetic) material with special interest lying at the point of criticality. Although this distribution consists of rules obtained from statistical ensemble physics this algorithm focuses on information only, treating the phys. quantities purely statistical. Hence, a new state is derived (sampled) if its entropically favorable due to the 2nd law of thermodynamics. At critical temperature the system starts to show order while the magnetization is non-zero. A magnetization measurement at low temperatures agrees with observations which shows that materials can get magnetized at low temperatures as they undergo a structural phase transition.

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

    var d=document,p,t,w,m,dH,v,cs,param={'T':T,'J':J};
    function u(){return Math.random()}function flip(node){node.value=`${-node.value}`}
    async function change(val,v,el){param[v]=val,el.innerHTML=`${val}`};
    function sleep(ms){return new Promise(function(resolve){setTimeout(()=>{resolve(0)},ms)})}
    function choice(arr){return arr[Math.round(u()*(arr.length-1))]}
    function mag(){let sum=0;for(let i=0;i<L;i++){for(let j=0;j<L;j++){sum+=parseFloat(m[i][j].value)}}return sum/L**2}
    async function metropolisHastings(){const i=Math.floor(u()*(L-1)),j=Math.floor(u()*(L-1)),cell=m[i][j],
    dH=2*param.J*cell.value*(1*m[(i+1)%L][j].value+1*m[(L+i-1)%L][j].value+1*m[i][(j+1)%L].value+
    1*m[i][(L+j-1)%L].value);if(dH<0||u()<Math.exp(-dH/(K*param.T))){flip(cell)}}
    async function render(){for(let i=0;i<L;i++){for(let j=0;j<L;j++){
    m[i][j].style.backgroundColor=C[S.indexOf(parseInt(m[i][j].value))]}}await sleep(5)}
    async function sim(){for(let i=0;i<N;i++){await metropolisHastings()
    if(i%R==0){await render();d.getElementById('mag').innerHTML=`Magnetization: ${mag()}`}}} 
    p=d.currentScript.parentNode,w=d.createElement('div'),m=[];t=d.createElement('table'); 
    t.style.width=p.style.width;t.style.height=p.style.width;t.style.fontSize="0";
    cs=`${(p.clientWidth/L)}px`;t.style.display="inline-block";p.appendChild(w); 
    w.appendChild(t);t.cellSpacing='0';t.style.padding='0';for(let i=0;i<L;++i){
    const row=d.createElement('tr'),r=[];row.style.borderSpacing='0';row.style.borderCollapse='collapse';
    t.appendChild(row);for(let j=0;j<L;j++){const col=d.createElement('td'); 
    col.name=`${j}`;col.style.display='inline-block';col.style.height=cs;
    col.style.width=cs;col.style.padding="0";if(I=='hot'){v=choice([0,1])}else{v=1}
    col.value=S[v].toString();row.appendChild(col);r.push(col)}m.push(r)} 
    const fd=d.createElement('div'),M=d.createElement('div');M.id='mag'; fd.appendChild(M);
    ['T','J'].forEach(x=>{const xName=x,l=d.createElement('label'),In=d.createElement('input'),
    out=d.createElement('output');l.innerHTML=xName;w.appendChild(fd); 
    if(x=='T'){In.min="0.001";In.step="0.01";In.max="50"}else{In.min="0.0001";In.step="0.0001";In.max="10"}
    In.type='range';In.id=xName;In.style.minWidth=t.clientWidth.toString()+'px';
    In.value=`${param[x]}`,In.oninput=function(){change(this.value,xName,out)};
    l.htmlFor=In.id;out.innerHTML=`${param[x]}`;[l,In,out,d.createElement('br')].forEach(
    e=>{fd.appendChild(e)});});sim()//_____Bogdan_Bermel______https://github.com/B0-B/________
</script>
```

## Performance
Browsers may experience some performance issues depending on the parameters set.
Default parameters were successfully tested in chromium & firefox.