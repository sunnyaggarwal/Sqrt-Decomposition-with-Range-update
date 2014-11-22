Sqrt-Decomposition-with-Range-update
====================================

c++ 
#include<bits/stdc++.h>
using namespace std;
#define ft first
#define sd second
#define pb push_back
#define mp make_pair
#define ll long long 
#define mod 1000000007
int N,SQRT,Q,sz;
vector< pair<int,int > > A;
vector< pair<ll,ll > > F,TEMP,NVAL;
vector< bool > FG;

void init(){
	A.resize(N+1);
	SQRT = 1;
	while(SQRT*SQRT<=N)
		SQRT++;
	SQRT--;
	sz = N/SQRT + (bool)(N%SQRT);
	F.resize(sz+1);
	FG.resize(sz+1);
	TEMP.resize(SQRT+1);	
	NVAL.resize(sz+1);
}


void build(){
	int j = 1;
	for(int i=1;i<=sz;i++){
		F[i].ft = 1;
		F[i].sd = 0;
		while(j<=N && (j-1)/SQRT+1  == i){
			F[i].ft = (F[i].ft * A[j].ft)%mod;
			F[i].sd = (F[i].sd * A[j].ft + A[j].sd)%mod;
			j++;
		}
		FG[i] = 0;
	}
}

void adjust(int L,int R,ll a, ll b){
	int start , end;
	start = ((L-1)/SQRT)*(SQRT)+1;
	end = min(N,((L-1)/SQRT+1)*(SQRT)); 	
	if(FG[(L-1)/SQRT+1]){
		for(int i=start ; i<=end ; i++){
			A[i].ft = NVAL[(L-1)/SQRT+1].ft;
			A[i].sd = NVAL[(L-1)/SQRT+1].sd;
		}
		FG[(L-1)/SQRT+1] = 0;	
	}
	for(int i=L;i<=R;i++){
		A[i].ft = a;
		A[i].sd = b;
	}
	F[(L-1)/SQRT+1].ft = 1; F[(L-1)/SQRT+1].sd = 0;
	for(int i=start ; i<=end ; i++){
		F[(L-1)/SQRT+1].ft = (F[(L-1)/SQRT+1].ft * A[i].ft)%mod;
		F[(L-1)/SQRT+1].sd = (F[(L-1)/SQRT+1].sd * A[i].ft + A[i].sd)%mod;
	}
}

ll func(int L,int R,ll x){
	if(FG[(L-1)/SQRT+1])
		adjust(L,R,NVAL[(L-1)/SQRT+1].ft,NVAL[(L-1)/SQRT+1].sd);
	for(int i=L;i<=R;i++)
		x = (A[i].ft * x + A[i].sd)%mod;		
	return x; 
}
void update(int L,int R,ll a,ll b){

	TEMP[0].ft = 1;
	TEMP[0].sd = 0;
	for(int i=1;i<=SQRT;i++){
		TEMP[i].ft = (TEMP[i-1].ft * a)%mod;
		TEMP[i].sd = (TEMP[i-1].sd * a + b)%mod;
	}
	if((L-1)/SQRT+1 == (R-1)/SQRT+1){
		adjust(L,R,a,b);
	}else{
		adjust(L,((L-1)/SQRT+1)*SQRT,a,b);
		adjust(((R-1)/SQRT)*SQRT+1,R,a,b);
		L = ((L-1)/SQRT+1)*SQRT+1;
		R = ((R-1)/SQRT)*SQRT;
		while(L<=R){
			FG[(L-1)/SQRT+1] = 1;
			NVAL[(L-1)/SQRT+1].ft = a;
			NVAL[(L-1)/SQRT+1].sd = b;
			F[(L-1)/SQRT+1].ft = TEMP[SQRT].ft;
			F[(L-1)/SQRT+1].sd = TEMP[SQRT].sd;
			L+=SQRT;
		}	
	}
}

ll query(int L,int R, ll x){

	ll val = x;
	if((L-1)/SQRT == (R-1)/SQRT){
		val = func(L,R,val);
	}else{
		val = func(L,((L-1)/SQRT+1)*SQRT,val);
		L = ((L-1)/SQRT+1)*SQRT+1;
		while((L-1)/SQRT != (R-1)/SQRT){
			val = (F[(L-1)/SQRT+1].ft * val + F[(L-1)/SQRT+1].sd)%mod;
			L += SQRT;
		}
		val = func(L,R,val);	
	}
	return val;	
}
int main(){

	cin >> N >> Q;
	init();
	for(int i=1;i<=N;i++) 
		cin >> A[i].ft >> A[i].sd;
	build();
	int c,L,R,a,b,x;
	while(Q--){
		cin >> c;
		if(c==1){
			cin >> L >> R >> a >> b;
			update(L,R,a,b);
		}else{
			cin >> L >> R >> x;
			cout << query(L,R,x) << endl;
		}	
	}	
	return 0;
}
