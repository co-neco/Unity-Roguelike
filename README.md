# Unity_Roguelike
some extra code which is necessary for more high version

I want to share something about some problems that I encountered in a game tutorial which is called Roguelike based of Unity.

1.The first issue I encountered is the function called protected override void OnCantMove<T>(T component).
  when I first use the function in Player.cs,the definition of this function is like this as below:
  
  protected override void OnCantMove<T>(T component){
  Wall hit=component as wall;
  hit.DamageWall(wallDamage);
  animator.SetTrigger(Player_Attacck);
  }
  
  Please notice the code is the same as the toturial which Unity provides.
  
 // ***************************************************************
  and if I run the game,Unity would display "NullReferenceException: Object reference not set to an instance of an object.
Player.OnCantMove[Wall](.Wall component...)".Because I'm a beginer of Unity,so I put this sentence in Baidu and Unity Community to find solutions,and most of the answers are that I may use Null reference(such as  some objects).
  I don't know where the issue is.Further more,the code is the same as the toturial.And after many attempts,I ultimately found the issue.Maybe I should think answers which peope gave in Unity community prefoundly.The issue is because of "Wall hit=component as wall;",since player doesn't only collide with 'Wall' class.Besides 'Wall',player may collide with enemy and 'OutWall',but "Wall hit=component as wall;" just only applys to 'Wall' class.So when player collides with enemy,the sentence 'component as Wall'  can't execute correctly.(Why it's wrong in y PC,I think it maybe the version's difference of Unity.)
  
    The solution is add some code as below:
  1.open the MoveObject.cs,and add 
  "
 
  [HideInInspector]public bool noFeedback = false;
  if ((hit.collider.tag != "InnerWall" && GameManager.instance.playersTurn == true)
          || (hit.collider.tag != "Player" && GameManager.instance.enemiesMoving == true))
            noFeedback = true;
            
  "
  
  when what player collides with isn't InnerWall,we shouldn't execute 'Wall hit=component as wall;' .Here I use noFeedback to judge whether what player collide with is InnerWall.
  
  2.open the Player.cs,and add
  "
  
   if (noFeedback == true)
        {
            noFeedback = false;
            return;
        }
        
  "
  when what player collide with isn't InnerWall,noFeedback is true.But in thenext time,player will collide with another object,so we should reset noFeedback to false.In this way,we can solve the problem.
  
