```
int betting() //Asks user amount to bet
{
 printf("\n\nEnter Bet: $");
 scanf("%d", &bet);

 if (bet > cash) //If player tries to bet more money than player has
 {
        printf("\nYou cannot bet more money than you have.");
        printf("\nEnter Bet: ");
        scanf("%d", &bet);
        return bet;
 }
 else return bet;
} // End Function
```

```
if(p>21) //If player total is over 21, loss
{
    printf("\nWoah Buddy, You Went WAY over.\n");
    loss = loss+1;
    cash = cash - bet;
    printf("\nYou have %d Wins and %d Losses. Awesome!\n", won, loss);
    dealer_total=0;
    askover();
}
```

코드에 헛점이 있다.

bet 을 음수로 하는 것에 필터가 없다.

`cash = cash - bet;`
이므로 큰 음수를 넣고 진다면 큰 돈을 얻게 될 것이다.
