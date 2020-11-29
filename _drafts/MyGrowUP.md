# 问题记录：

Q1. 收到大约第4个左右的RA包时，用户端程序才能正确的获取到包内容。
    A: DHCP6 client application will resend the Route-Solicit. This is because the DHCP6 client timer expired because the NTP service started working.

Q2. How to get a biggest and smallest number.
    A: u32 min_score; min_score = ~(u32)0;

    ```c
    #include <stdio.h>

    int main()
    {
        unsigned int min;
        
        min = ~(unsigned int)0;

        printf("The min1 = %d\n", min>>1);
        printf("The min2 = %d\n", min>>1); 

        return 0;
    }

    // The min1 = -1
    // The min2 = 2147483647


    #include <limits.h>
    #include<stdio.h>
    int max = INT_MAX;//最大数 
    int min = INT_MIN;//最小数 
    int main(){
	printf("max = %d\tmin = %d\n", max, min);
    printf("max = %d\tmin = %d\n", __INT_MAX__, __INT_MAX__);
	return 0;	
}
    ```
