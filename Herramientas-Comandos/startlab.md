Es una funcion para la zshrc que cree con chatgpt para automatizar todo el reconocimiento principal de las maquinas

-----

```zshrc
# --- Función auxiliar para obtener puertos en formato nmap -p ---
sacarpuertos() {
  if [[ ! -s "$1" ]]; then
    echo ""
    return
  fi
  ports="$(cat "$1" | grep -oP '\d{1,5}/open' | awk '{print $1}' FS='/' | xargs | tr ' ' ',')"
  echo "$ports"
}

# --- startlab: startlab <ip> <nombremaquina> ---
startlab() {
  local ip="$1"
  local name="$2"

  if [[ -z "$ip" || -z "$name" ]]; then
    echo "Uso: startlab <ip> <nombremaquina>"
    return 1
  fi

  # Paths
  local base_dir="$PWD/$name"
  local content_dir="$base_dir/content"
  local nmap_dir="$base_dir/nmap"

  # Crear carpetas
  mkdir -p "$content_dir" "$nmap_dir" || { echo "Error creando carpetas"; return 1; }

  # Entrar a content
  cd "$content_dir" || { echo "No se pudo entrar a $content_dir"; return 1; }

  # Ejecutar settarget si existe
  if command -v settarget >/dev/null 2>&1; then
    echo "[*] Ejecutando: settarget $ip $name"
    settarget "$ip" "$name" || { echo "settarget devolvió error"; return 1; }
  else
    echo "Aviso: comando 'settarget' no encontrado en PATH. Saltando settarget."
  fi

  # 1) ping -c 1 ip
  echo "[1/6] Ejecutando ping a $ip..."
  ping -c 1 "$ip"   # muestra todo el output normalmente
  if [[ $? -eq 0 ]]; then
    echo "[*] Ping exitoso a $ip"
  else
    echo "[!] Ping falló para $ip"
    return 1
  fi

  # 2) nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn ip -oG allPortsTCP
  local allTCP="$nmap_dir/allPortsTCP"
  echo "[2/6] Ejecutando: nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn $ip"
  nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn "$ip" -oG "$allTCP" || { echo "nmap full TCP falló"; return 1; }

  # 3) extraer puertos TCP usando sacarpuertos
  local tcp_ports
  tcp_ports="$(sacarpuertos "$allTCP")"
  if [[ -z "$tcp_ports" ]]; then
    echo "No se encontraron puertos TCP en $allTCP. Continuando sin lista de puertos."
  else
    echo "Puertos TCP encontrados: $tcp_ports"
  fi

  # 4) nmap -sCV -p<puertos> -oN targetTCP
  local targetTCP="$nmap_dir/targetTCP"
  echo "[3/6] Ejecutando: nmap -sCV ${tcp_ports:+-p $tcp_ports }-oN $targetTCP $ip"
  if [[ -n "$tcp_ports" ]]; then
    nmap -sCV -p "$tcp_ports" -oN "$targetTCP" "$ip" || { echo "nmap -sCV TCP falló"; return 1; }
  else
    nmap -sCV -oN "$targetTCP" "$ip" || { echo "nmap -sCV TCP (sin -p) falló"; return 1; }
  fi

  # 5) nmap -sU -n -Pn --top-ports ip -oG allPortsUDP
  local allUDP="$nmap_dir/allPortsUDP"
  echo "[4/6] Ejecutando: nmap -sU -n -Pn --top-ports 200 $ip"
  nmap -sU -n -Pn --top-ports 200 "$ip" -oG "$allUDP" || { echo "nmap top UDP falló"; return 1; }

  # extraer puertos UDP usando sacarpuertos
  local udp_ports
  udp_ports="$(sacarpuertos "$allUDP")"
  if [[ -z "$udp_ports" ]]; then
    echo "No se encontraron puertos UDP en $allUDP. Continuando sin lista de puertos UDP."
  else
    echo "Puertos UDP encontrados: $udp_ports"
  fi

  # 6) nmap -sU -sCV -p<puertos> -oN targetUDP
  local targetUDP="$nmap_dir/targetUDP"
  echo "[5/6] Ejecutando: nmap -sU -sCV ${udp_ports:+-p $udp_ports }-oN $targetUDP $ip"
  if [[ -n "$udp_ports" ]]; then
    nmap -sU -sCV -p "$udp_ports" -oN "$targetUDP" "$ip" || { echo "nmap -sU detallado falló"; return 1; }
  else
    nmap -sU -sCV -oN "$targetUDP" "$ip" || { echo "nmap -sU detallado (sin -p) falló"; return 1; }
  fi

  echo "[6/6] startlab completado. Resultados en: $nmap_dir"
  return 0
}
```
