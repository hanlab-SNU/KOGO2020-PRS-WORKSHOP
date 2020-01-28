# The 16th KOGO Winter Symposium WORKSHOP2
## Polygenic Risk Score (PRS) Session 2
### 2020-02-06 실습시간 | 16:40~17:00 (20')

Installation
--------------------------------------------------------
#### Installing [PLINK v.1.9](https://www.cog-genomics.org/plink2)
 * Windows : [64bit](http://s3.amazonaws.com/plink1-assets/plink_win64_20200121.zip) |  [32bit](http://s3.amazonaws.com/plink1-assets/plink_win32_20200121.zip)
 * Mac OS : [다운로드](http://s3.amazonaws.com/plink1-assets/plink_mac_20200121.zip)
 * Linux : [64bit](http://s3.amazonaws.com/plink1-assets/plink_linux_x86_64_20200121.zip) |  [32bit](http://s3.amazonaws.com/plink1-assets/plink_linux_i686_20200121.zip)

환경변수를 추가하면 cmd(명령 프롬프트)나 terminal에서 어느 위치에서나 `. /절대경로/plink(.exe)` 대신 `plink` 만으로 실행이 가능합니다.


 * Windows : 제어판 > 고급 시스템 설정 > 환경 변수 > 시스템 변수 > 편집 또는 새로 만들기 > 설치된 `PLINK`의 PATH 추가
 * Mac / Linux : `.bashrc`/`.bash_profile` 설정 변경;
                 mac중 기본 shell을 zsh로 사용하는 분들은 (OS X Catalina) `.zshrc` 설정 변경
 
   ```
   $ echo 'export PATH=$PATH:.' >> ~/.bashrc                    # ./ 없이도 실행 가능
   $ echo 'export PATH="$PATH:/프로그램 경로"' >> ~/.bashrc        # 설치된 경로 환경변수 추가
   $ source ~/.bashrc                                           # 환경변수 변경사항 적용
   ```
혹시 환경변수 추가를 진행하시다가 어려움을 겪으시는 분들은 당일 조교들에게 요청하시거나 또는 데이터가 있는(`./KOGO2020-PRS-WORKSHOP/data`) 폴더로 `PLINK` 실행파일을 옮기고 `./plink`로 실행하시면 됩니다.

 [conda](https://docs.anaconda.com/anaconda/install/)가 설치되어 있으면 command로도 설치가 가능합니다.

`conda install -c bioconda plink`

#### Installing R
 * Windows : [다운로드](https://cran.r-project.org/bin/windows/base/R-3.6.2-win.exe)
 * Mac OS : [다운로드](https://cran.r-project.org/bin/macosx/R-3.6.2.pkg)
 * Linux : [다운로드](https://cran.r-project.org/bin/linux/)

#### Installing R Studio : 
 * Windows : [다운로드](https://download1.rstudio.org/desktop/windows/RStudio-1.2.5033.exe)
 * Mac OS : [다운로드](https://download1.rstudio.org/desktop/macos/RStudio-1.2.5033.dmg)
 * Linux : [다운로드](https://rstudio.com/products/rstudio/download/#download)

#### Installing R package for visualization
```
# type to R console
> install.packages("ggplot2")
> library(ggplot2)
```

R과 R Studio는 PRS 분석 이후 visualization을 위함으로 굳이 설치하지 않으셔도 제공된 `.html`을 통하여 코드와 결과 figure를 보실 수 있습니다.


Data
--------------------------------------------------------
데이터는 git clone ([git](https://git-scm.com/book/ko/v2/%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0-Git-%EC%84%A4%EC%B9%98) 설치 필수) 또는 가장 오른쪽 위에 있는 Clone or download 초록색 버튼 클릭 > [Download ZIP](https://github.com/hanlab-SNU/KOGO2020-PRS-WORKSHOP/archive/master.zip)을 눌러서 다운로드 할 수 있습니다.

`$ git clone https://github.com/hanlab-SNU/KOGO2020-PRS-WORKSHOP.git`

`./data/` 폴더 내 두 개의 zip 파일을 unzip 시켜 주시기 바랍니다. (메일을 통해 전달된 pdf내 비밀번호 참고)  

`PRS_example.zip` 내 파일들
 ```
 # 파일 포맷 설명
 * .bfile (.bed/.bim/.fam) : PLINK binary input fileset (N=200)
 * .assoc : CAD(Coronary artery disease) GWAS summary statistic file

* .log : PLINK log 파일
 * range_list : threshold range
 * SNP.pvalue : assoc 결과에서 SNP과 p.value만 뽑은 파일
 * .valid.snp : clumping된 결과(.clumped)에서 나온 3번째 column(SNP) 파일
 ```
 
`result.zip` 내 파일들
 ```
 * .*.profile : --score와 --q-score-ragne를 통해 나온 각 range별 polygenic score 결과
 * .clumped : --clump 이후 결과 파일
 * .nopred : --score의 input file 내 problems (non-dosage)
 * .html : profile 결과를 R을 통해서 visualization 진행한 코드 및 figure 결과
 ```
 
Instruction
--------------------------------------------------------
`PLINK`는 PRS 계산을 위한 소프트웨어는 아니지만, standard **C+T**(**C**lumping & P-value **T**hresholding SNPs) 방법으로 계산할 수 있습니다.

**Step 1. Clumping**
 ```
 $ plink \
    --bfile PRS_example \
    --clump-p1 1 --clump-r2 0.1 --clump-kb 250 \
    --clump Howson.CAD.assoc --clump-snp-field rsid \
    --clump-field p --allow-no-sex \
    ––out PRS_example
 ```

**Step 2. Generate PRS score**
 ```
 $ plink \
    --bfile PRS_example \
    --score Howson.CAD.assoc 1 3 6 header \
     --q-score-range range_list SNP.pvalue \
     --extract PRS_example.valid.snp --allow-no-sex \
     --out PRS_example
 ```

P.S. 이 때 `PLINK`의 환경변수를 추가하지 않으신 분들은 다운로드 받은 **PLINK 실행파일**의 경로와 **데이터**의 경로를 잘 제시하여 실행시켜주시기 바랍니다.
(실행파일과 데이터의 위치를 한 폴더 내에 넣어 주신 후 `./plink`로 실행하는 것을 추천드립니다.)

Reference
--------------------------------------------------------
1. PLINK v1.9 | Purcell, Shaun, et al. "PLINK: a tool set for whole-genome association and population-based linkage analyses." The American journal of human genetics 81.3 (2007): 559-575. [doi:10.1086/519795](https://doi.org/10.1086/519795)
2. GRASP CAD summary statistics | Howson JMM et al. Fifteen new risk loci for coronary artery disease highlight arterial-wall-specific mechanisms. Nat Genet. 49(7): 1113-1119. doi: 10.1038/ng.3874. [doi:10.1038/ng.3874](https://10.1038/ng.3874)
3. WTCCC Phase 1 data | Wellcome Trust Case Control Consortium. "Genome-wide association study of 14,000 cases of seven common diseases and 3,000 shared controls." Nature 447.7145 (2007): 661. [doi:10.1038/nature05911](https://doi.org/10.1038/nature05911)

Q&A
--------------------------------------------------------
관련 질문이나 기타 의견 사항이 있으시면 언제든지 e-mail로 연락 바랍니다. : [hanlab.snu@gmail.com](mailto:hanlab.snu@gmail.com)

[한범 교수님 연구실 homepage](http://hanlab.snu.ac.kr/)
